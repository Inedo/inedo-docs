---
title: "Semantic Versioning for Containers"
order: 4
---

ProGet can enforce "semantically versioned" tags (i.e. `3.4.1`, `2.4.1-beta.1`). This eliminates the confusion of "loosely versioned" tags (like `v6` and `v6-latest`) and prevents wasted time and other problems that come from using the wrong version of a container image.

:::(info)
Container Versioning is available on paid versions of ProGet. 
:::

## Tags vs Versions in Docker
In a Docker repository, container images are not versioned; instead, they are "tagged," and it is up to the publisher to determine which container images have which tags and when those tags change. For example, some publishers use "v6" to indicate the latest version, while another publisher uses "4.2-stable" to indicate a non-beta version of a container.

To make matters worse, publishers can change their tags at any time, which leads to a lot of confusion when container versions change depending on the day of installation. Ultimately, this leads to a lot of lost productivity and even production failures in organizations when the wrong containers are tested and deployed.

This is where ProGet's Container Versioning feature comes in. Instead of allowing publishers to specify arbitrary version numbers, they are limited to using standardized version numbers and cannot change these versions after release.

## How to Enable Semantic Versioning for Containers
Container Versioning may be enabled on a registry-by-registry basis on the Manage feed page.

![Configure Versioning](/resources/docs/proget-docker-configureversioning.png){height="" width="50%"}

After activation, ProGet only allows tags that conform to the three-part SemVer format (e.g. 3.4.1, 4.0.1, 2.4.1-beta.1, etc.) or are listed in the "Permitted tags" list. Note that existing tags in the repository are not affected by this, nor are tags coming via feed replication.

## Virtual Release Number Tags

When Container Versioning is enabled, ProGet automatically manages certain tags for you.

The `latest` tag refers to the highest version number in your repository, regardless of whether it was the most recent container image tagged. For example, if you tag an image with `2.8.4`, but the highest tag in your repository is `3.1.0`, then the `latest` tag will still refer to `3.1.0`.

ProGet also maintains one- and two-part version number tags (e.g. `3.4` or `4`), and  assigns to images when a container image is tagged with a stable release tag. For example, if you tag a container image with `3.4.1`, ProGet will also tag that image with `3.4` and remove `3.4` from all other container images (such as `3.4.0`).

## Retagging Container Images

Like with [repackaging your packages](/docs/proget/packages/repackaging), you should  "retag" a container image from a pre-release version (e.g. `3.4.1-rc.4`) to a stable version (`3.4.1`) once it's passed testing. 

The Docker API does not have an API for adding a tag to an image, but it's something we're considering doing. Please contribute to the [API to apply an Alternate Tag to Docker Container Image](https://forums.inedo.com/topic/3255) discussion if you're interested in this feature.

In the meantime, you can simply download the manifest, and reupload it as a new version. 

### Example PowerShell Script to Repackage Containers
*Thanks to Stephen Shaff*. 
```powershell
function RepackageContainerImage() {

  param (
      [string] $packageName = $(throw "-packageName is required.  This is the namespace/image-name"),
      [string] $fromFeed = $(throw "-fromFeed is required"),
      [string] $toFeed = $(throw "-toFeed is required"),
      [string] $fromVersion = $(throw "-fromVersion is required.  This is the current tag on the image in the fromFeed."),
      [string] $toVersion = $(throw "-toVersion is required.  This is the tag to be applied after the image is promoted."),
      [string] $comments = "Promoted by automation",
      [string] $bearerToken = $(throw "-bearerToken is required"),
      [string] $progetBaseUrl = $(throw "-progetBaseUrl is required")
  )

  $postBody = @{
      packageName="$packageName";
      groupName="";
      version="$fromVersion";
      fromFeed="$fromFeed";
      toFeed="$toFeed";
      comments="$comments"
  } 
  # Promote the Container Image
  Invoke-WebRequest -Uri "$progetBaseUrl/api/promotions/promote" -Method POST -Body $postBody -Headers @{Authorization=("Bearer {0}" -f $bearerToken)}

  # Retag the container image by downloading the manifest and then re-uploading it as the new version
  $manifest = Invoke-WebRequest -Uri "$progetBaseUrl/v2/$toFeed/$packageName/manifests/$fromVersion" -Method GET `    
        -Headers @{Authorization=("Bearer {0}" -f $bearerToken);"accept"="application/vnd.docker.distribution.manifest.v2+json"}    
  Invoke-WebRequest -Uri "$progetBaseUrl/v2/$toFeed/$packageName/manifests/$toVersion" -Method PUT -Body $manifest.ToString() `
       -Headers @{Authorization=("Bearer {0}" -f $bearerToken);"content-type"="application/vnd.docker.distribution.manifest.v2+json"}
}
```
### Example PowerShell Script to Authenticate to Docker
Docker's API uses bearer-based authentication, which means you'll need to generate a "bearerToken" from your API key. The script below (also from Stephen Shaff) can do that:
```powershell
function GetDockerToken() {

    param (
        [string] $packageName = $(throw "-packageName is required.  This is the namespace and image name.  For example: library/my-container-image"),
        [string] $feed = $(throw "-feed is required"),
        [string] $actionToAuthorize = $(throw "-action is required.  This is the docker action to be authorized (pull, push, delete, etc)"),
        [string] $apiKey = $(throw "-apiKey is required"),       
        [string] $progetBaseUrl = $(throw "-progetBaseUrl is required. "),
	[string] $service	
    )


	if ($service -eq "") {
          # This expects that $progetBaseUrl is prepended with "https://"  If you are using "http://" then change 8 to 7 below.
	  $service = $progetBaseUrl.SubString(8,$progetBaseUrl.Length-8)
	}
	
	$base64AuthInfo = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f "api","$apiKey")))
	$response = Invoke-WebRequest -Uri "$progetBaseUrl/v2/_auth?service=$service&scope=repository`:$feed/$packageName`:$actionToAuthorize" -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} 
	if ($response.StatusDescription -eq "OK") {
		$token = ($response.Content | ConvertFrom-Json).token
		$token
	}
}
```

Here is how to use the `GetDockerToken` to get a token, and then use it in a different API call.

```powershell
$bearerToken = GetDockerToken -feed $toFeed -packageName $packageName -actionToAuthorize "pull" -apiKey $apiKey -progetBaseUrl $progetBaseUrl
$manifest = Invoke-WebRequest -Uri "$progetBaseUrl/v2/$fromFeed/$packageName/manifests/$fromVersion" -Method GET -Headers @{Authorization=("Bearer {0}" -f $bearerToken)}
```