---
title: Updating Documentation using nDocs
max-header-level: 3
hidden: true
---

# Updating Documentation using nDocs

This guide will show users how to make changes to Inedo documentation.  Inedo uses a in-house CMS system called nDocs.  nDocs is a .NET web application that leverages CommonMark markdown syntax and HTML to provide a rich documentation experience for their customers.

::: (Info) (Living Documentation)
This is expected to be living documentation that is updated as improvements are made and new canon is discovered.
:::

## Prerequisites
There is not much needed to start working with nDocs.  The three main components are:
- git ([https://git-scm.com/](https://git-scm.com/))
- MarkDown Editor (reccomended: Visual Studio Code: [https://code.visualstudio.com/](https://code.visualstudio.com/))
- nDocs local server

Other tools may be used, but not fully supportted:
- GitHub Desktop ([https://github.com/apps/desktop](https://github.com/apps/desktop))
- Markdown Monster ([https://markdownmonster.west-wind.com/](https://markdownmonster.west-wind.com/))

### Visual Studio Code
Although Visual Studio Code (VSCode) has a lot of built-in functionality.  There are some very helpful extensions to help improve the development experience:
- **Code Spell Checker** by *streetsidesoftware.com*
   - General spell checker
- **learn-markdown** by *Microsoft*
   - Includes extra MarkDown tools like special syntax highlighting for common markdown controls, preview file, keyboard shortcuts, etc...
- **YAML** by *redhat.com*
   - YAML syntax to help define the page metadata
   
## Setting up your Development Environment
The development environment set up is broken down into two components; cloning the [inedo-docs](https://github.com/inedo/inedo-docs) GitHub repository and running the nDocs Local Server.

### Cloning the Docs Content From GitHub
Once the tools are installed, you will need to first clone the [inedo-docs](https://github.com/inedo/inedo-docs) GitHub repository to "`C:\Projects\inedo-docs`".  If you do not have a "`C:\Projects`" directory, first create that folder then clone your repository.

::: (Info) (Note:)
If you want to use a different folder for your git repository, you will need to update the [`MarkdownPath` configuration value](#config-file) in the nDocs local server.
:::

To clone the git repository, you will need to run the following command from an administrative command prompt:

```bash
cd C:\projects
git clone https://github.com/Inedo/inedo-docs.git
```

### Running the nDocs Local Server
Once you have the repository checked out, you will need to download the latest nDocs Server from ProGet 1000. For simplicity sake, I would extract the server to "`C:\Projects\nDocs-server`".  Then simply run `nDocs.WebApplication.exe`.  

::: (Warn) (Important!)
Do not extract the nDocs local server into your content directory!
:::

When the program first starts, you will see the url that nDocs is hosted on.  The URL will be listed after the statement "`Now listening on:`".  Open your browser and navigate to that URL.  Now anytime a change is made to your markdown, the nDocs site will show your change.

Example Output:
```bash
info: Inedo.Web.BackgroundTaskQueueService[0]
      Background Task Queue is starting.
info: Microsoft.Hosting.Lifetime[14]
      Now listening on: http://localhost:5000
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Production
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\Projects\nDocs-server
```

## Making Documentation Changes
Documentation changes should always be made on a new branch in git and a pull request must be submitted for review.  The process for making documentation changes:

1. Switch to master branch (if not already on it).
2. Pull latest changes.
3. Create a new branch and switch to branch.
4. Make changes **NOTE: If you change a file name and/or folder name, make sure to add a redirect to the redirects.xml for the old URL to the new URL**
5. Commit and push changes to new branch
6. Create a [pull request in GitHub](#pull-requests) to merge your changes in.  
7. Assign a reviewer to the pull request
8. Rich or Alex will approve pull request, delete branch, and deploy inedo-docs application in BuildMaster to production

::: (Warn) (Important!)
Changes should never be made on the **master** branch.  All changes should be made on a feature branch and a [pull request](#pull-requests) **must** be used to merge changes back to the `master` branch.
:::

### Page File Layouts
Currently nDocs supports two formats of pages; MarkDown and HTML.  Markdown files end with `.md` and HTML pages end with `.html`.  Files are made up of two components; a YAML heading and body content.  A URL will be generated for a file based on the path relative to `C:\Projects\inedo-docs\Content`. For example, a MarkDown file that is stored at `C:\Projects\inedo-docs\Content\proget\installation\installation-guide.md` will be accessed using the URL `/docs/proget/installation/installation-guide`.

#### Metadata
::: (Info) (Note:)
No properties are required to create a page.
:::

Metadata for a file is formatted using YAML syntax and is specified at the root of the file in the format:
```YAML
---
property: value
property: value
---
```

Available Properties:

| Property | Type | Description |
| -------- | ---- | ----------- |
| `title` | `string` | Title that will show in the tab, navigation tree, and H1 (default uses the file name) |
| `nav-title` | `string` | Title that will show in the navigation tree (**overrides `title`**) (default uses the title) |
| `max-header-level` | `int` | The max header level to show in the table of contents (default is 2) |
| `masterpage` | `string` | The name of the master page file to use for the page layout.  This can be a global or a side-by-side path (default is DocsArticlePage.html) |
| `icon-url` | `string` | The URL of the icon to show in the navigation tree (default null) |
| `url-slug` | `string` | Overrides the URL of the page **Should be used on limited basis and requires approval** (default uses standard path) |
| `order` | `int` | The order the page should show in the navigation (default is 1000) |
| `hidden` | `true|false` | A true value will hide the page when in production (default is false) |

#### Folders
Folders are considered URL segments. Folders will show in the navigation tree when there are MarkDown or HTML files within the folder or when a `#.md` or `#.html` file exists.  Metadata for the folder will be contained in the `#` file.  

To make a folder also work as a page, add body contents to the `#` file.  When body contents are included, the URL will be the folder path relative to `C:\Projects\inedo-docs\Content`. For example, for a folder with body contents stored at `C:\Projects\inedo-docs\Content\proget\installation\#.md` will be accessed using the URL `/docs/proget/installation`.

::: (Info) (Note:)
If there are no visible `.md` or `.html` files within the folder and it has a `#` file with body contents, the folder will show as a page, not a group, in the Navigation Tree.
:::

### MarkDown
MarkDown is specified using the CommonMark language syntax.  nDocs use the [MarkDig NuGet Package](https://github.com/xoofx/markdig) with a number of extensions to display MarkDown pages.

#### Info Blocks Syntax
Info Blocks are a custom Indeo MarkDown block that can be specified in the following format:
```bash
::: (Type) (Title)
Body
:::
```

The `Type` value can be:
- Info
- Warn
- Error
- Success
- Internal

The `Title` cannot contain any markdown.

The `Body` can contain MarkDown.

### HTML
HTML files should only contain the contents of the page.  It **should not** include the `<html>`, `<head>`, or `<body>` tags in it.  HTML files can have YAML metadata at the top.

#### Info Blocks Syntax
Info blocks can be used in HTML, but there entire HTML will need to be specified:
```html
<div class="info-content-box info">
   <div class="title">The Title</div>
   <div class="content">Body contents</div>
</div>
```

### MasterPages
MasterPages contain the main page layout for the pages.  They can be stored either in the global folder (`C:\Projects\inedo-docs\MasterPages`) or as a side-by-side resource.  The master page can be overwritten by specifying the path `masterpage` property in the page metadata. The main two master pages are:
- DocsArticlePage.html (default for all pages)
- HomePage.html

Although a style block can be added to these pages, all styles should be submitted as a task to engineering to implement in the theme.  nDocs is much more efficient at hosting CSS compared to a style block on the page.

::: (Warn) (Note:)
Before making changes to a MasterPage, get guidance from Rich first.
:::

### Home Page
The home page is a special file stored at `C:\Projects\inedo-docs\Content\home.md`.  This file is the one exception to having it's own styles and master page.  This file cannot be renamed, but the title can be updated in the page metadata.

### Images
Images can be stored either in the global folder (`C:\Projects\inedo-docs\Resources`) or as a side-by-side resource.  Images stored in the global resources folder will be using `/resources/docs/file.extension` (example: `/resources/docs/buildmaster-install.png`).  If you use sub folders within the global folder, those will be treated as slugs in the URL.  For example, an image stored at `C:\Projects\inedo-docs\Resources\BuildMaster\Install\buildmaster-install.png` will be accessible using `/resources/docs/buildmaster/install/buildmaster-install.png`.

Images stored side-by-side with the content should use `/docs/path/to/file.extension`.  For example, if the file is stored at `C:\projects\inedo-docs\content\BuildMaster\buildmaster-install.png` the URL would be `/docs/buildmaster/buildmaster-install.png`.

### Pull Requests
When making changes to our documentation, all changes must be made using a feature branch and a pull request must be used to  merge changes back to the `master` branch.

#### Maintainer Process
The pull request will work as follows:
1. Make all commits to your feature branch
2. Create a [pull request](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/creating-a-pull-request)  on GitHub using a short readable description of the change as the name
    1. Make sure to pay attention to the arrow when selecting your branches to ensure changes are merged the correct way (the target branch is to the left)
1. Assign a reviewer to approve the pull request changes

::: (Info)
Make sure to create clear and usable commit messages that explain what the changes include.  These commit messages are public and our entire user community will see them.
:::

#### Approver Process
When approving a pull request, the reviewer will have the responsibility to ensure the documentation changes match the [required documentation canon](#development-canon), approving or rejecting request, and completing the pull request by merging the  changes back to the `master` branch.  If a pull request is rejected, a comment will be left for the maintainer to either correct their changes or cancel the pull request and scrap the rejected changes.

The process for completing a pull request will work as follow:
1. Approve the changes for the pull request
2. Click the "Squash and Merge" button option to merge in changes
    1. If multiple commits are made, the commit messages will need to be merged into a single commit message on the confirmation page
3. Confirm squash and merge
4. Delete the source branch

## Renaming Files & Folders
::: (Warn) (Important!)
When you rename a file or folder, this will change the URL for that page.  
:::

When a URL is changed for a page, you will need to add a [redirect rule](#adding-redirects) to link from the old URL to the new URL.  You will also need to update any pages that link to that old URL as well.  The [link scanner](#link-scanner) can help you find all the pages that use that link.  You can also search in all files using Visual Studio Code or a tool like File Locator Pro.

## Adding redirects
All redirects are stored at `C:\Projects\inedo-docs\redirects.xml`.  To add a new redirect, add a new `redirect` node under the root `redirects` with the `from`, `to`, and `modified` attributes.

Example:
```xml
<redirect from="/docs/otter-global-components-rafts" to="/docs/otter/scripting-in-otter/otter-rafts-and-git-storage" modified="2/25/2022 12:00:00 AM" />
```

Redirects are exact URL matches.  If there is a need for partial matches, please reach out to Rich for a feature request.

### Custom CloudFlare Logic
When the old docs site was migrated to Document360, we had a rule in CloudFlare that would convert any URL from folder based (`/docs/path/to/page`) to a slug (`/docs/path-to-page`).  This has been modified in migrated to the nDocs server.  

When a page URL is not found (either in the folder structure or in the redirect file), nDocs will automatically convert from the folders-based URL to the slug based URL.  This may cause confusion when developing new pages.  If this happens, please check that a page exists at the URL you are specifying or add a redirect rule as needed.

### Link Scanner
The Link Scanner detects broken links, links that are redirected, and links to hidden pages within the docs content.  This scan is ran anytime the nDocs Local Server indexes the pages to build the navigation tree.  When development mode is enabled and issues are detected, a message containing links to the problem pages will show at the top of every page.  This allows issues to be found at development time, prior to running Screaming Frog.  When navigating to a page that contains issues, each link's text and URL will also be shown at the top of the page.

The link scanner does **not** find broken image links, broken external links, broken links in the home page, or broken links that are contained on the master pages.  Screaming Frog will still be ran weekly to find broken internal links, broken image links, and broken external links.

## Config file

The config file is stored side-by-side with the nDocs local server in a file named `appsettings.json`.  The main values that can be changed are under the `Inedo.Settings` section of config.  The only two properties that should ever be changed are:
- `MarkdownPath`: The path that the inedo-docs GitHub repo is checked out to.
- `IsDevelopment`:  Changing this value to false, will hide all hidden pages, hide all internal info blocks, show custom error pages, and will enable caching.  This can be used to test what the site will look like in production.

## Development Canon

### Headings
- Never start your page with an H1, nDocs will automatically add an H1 based on the page metadata title or file name if the title is not specified.
- If an ID is not specified for a header, an ID will be generated based on the text within the header
- When using Markdown
    - Headings must be specified in Markdown syntax instead of HTML syntax
        - If HTML headings are used in Markdown, they will not show in the table of contents
    - If a custom ID is needed in a Markdown heading, append `{ #heading-id }` to the end of the heading
        - Example: `## Using Feed Connectors { #feed-connectors }`

### Code Blocks
- Code blocks should always have a language applied to them (e.g ` ```bash `)
    - This is to ensure that code blocks are displayed consistently
    - Use the language most applicable to the code (e.g. `yaml`, `xml`, `html`, etc.)
    - When a language does not appear applicable (e.g. CLI commands) use `bash`
        - `plaintext` can also be used in the event that bash is not suitable

### Links
- Links should **always** be with /docs/url/to/file (example: If the file is stored at `C:\Projects\inedo-docs\Content\BuildMaster\overview.md` the URL would be `/docs/buildmaster/overview`)
  - Be sure to **not** include the file extension (.md/.html) in the URL
  - Never link to https://docs.inedo.com/docs/...

### Images
- Images can be stored side-by-side with the file or in the global resources folder
   - The global resources folder is `/resources/docs/filename.extension` (example: `/resources/docs/buildmaster-install.png`)
   - Side-by-side references should use `/docs/path/to/file.extension` (example: if the file is stored at `C:\projects\inedo-docs\content\BuildMaster\buildmaster-install.png` the URL would be `/docs/buildmaster/buildmaster-install.png`)
- Image file names should be named as to be easily identifiable. 
    - Typically this means naming them with a combination of the Inedo product, page and description of the image
    - For example, an image depicting the feeds page, indicating where to click to create a new feed in ProGet could be named `proget_feeds_create_new_feed.png`
- When adding markdown to insert an image, it should be followed with code to decrease the image by 50%, using `{height="" width="50%"}`

### Screenshot formatting
- When taking screenshots of Inedo products, the following should be applied where possible:
    - Omit the header and footer of the page (e.g. the quicklink bar at the top and the bar at the bottom that contains the version, user, etc.)
        - Only include the header if it's relevant to the purpose of the image (e.g. instructing the user to select the settings icon)
    - Omit the scroll bar on the right if it's present
    - White space should be kept to a minimum, resizing the window where needed
- Example:

![Screenshot](/resources/docs/proget-policies-sharedpolicy-blocked.png){height="" width="50%"}

- When taking screenshots of modal pop ups:
    - some may have significant whitespace by default. Resize them to reduce this where possible.
    - Include the grey outline of the modal
- Example:

![Modal](/resources/docs/proget-chocolatey-privatename.png){height="" width="50%"}