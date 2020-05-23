---
title: Clair
subtitle: Clair
sequence: 500
keywords: proget, vulnerabilities, clair
show-headings-in-nav: true
---

[Clair](https://github.com/quay/clair) is an open source tool used for the static analysis of vulnerabilities in application containers, managed under [Red Hat's Quay project](https://github.com/quay/quay). This feature is available in the paid and trial editions of [ProGet 5.3](https://inedo.com/proget/pricing) and later.

## Installing and Configuring the Clair Server {#install data-title="Install and Configure"}

Clair is typically hosted as an on-premise API and requires a Postgres database to manage its vulnerabilities. You can install and run Clair two different ways: 1) as a container-based installation using [Docker](https://github.com/quay/clair/blob/master/Documentation/running-clair.md#docker) or [Kubernetes](https://github.com/quay/clair/blob/master/Documentation/local-development.md) or 2) as a [self-hosted Go application](https://github.com/quay/clair/blob/master/Documentation/running-clair.md#source). 

Docker is the most common way of running Clair; to run Clair with Docker:

{.docs}
1. Create a local Clair config folder: `mkdir $PWD/clair_config`
2. Download a sample config that can be customized to fit your needs: `curl -L https://raw.githubusercontent.com/coreos/clair/master/config.yaml.sample -o $PWD/clair_config/config`
3. Run a new Postgres instance: `docker run -d -e POSTGRES_PASSWORD="" -p 5432:5432 postgres:9.6`
4. Run a new Clair instance: `docker run --net=host -d -p 6060-6061:6060-6061 -v $PWD/clair_config:/config quay.io/coreos/clair:v2.1.2 -config=/config/config`

_Note: The port mappings may need to be customized to fit your environment. Make sure that you modify the config file to point to the proper Postgres instance and password._

Once Clair is running, it will automatically connect to the vulnerability sources defined in the config file and download the needed vulnerability detection information. Clair requires Internet access to connect to the different vulnerability sources that keep its vulnerability detection information up-to-date.

## Installing the Clair Extensions {#extension data-title="Clair Extension"}

Integrating Clair with ProGet is easy: Simply navigate to the _Administration > Extensions_ page in your instance of ProGet and click on the Clair extension to install it.

## Configuring Clair in ProGet {#configureproget data-title="Clair in ProGet"}

To connect ProGet to Clair, you will first need to create a vulnerability source in _Administration > Vulnerability Sources > Create vulnerability source_, then select the _Clair Index_. You will then need to give the source a name and specify the host and port for your Clair instance (i.e., http://localhost:6060/). 

Optionally, you can specify an `API Authorization Header` for your Clair authentication method. Currently, we do not support any specific authentication method in Clair; whatever value you put in the `API Authorization Header` value will be passed to the Authorization property in the Clair API request.

:::attention {.best-practice}
You must set the Web.BaseUrl to the URL of your ProGet server in _Administration > Advanced Settings_ in order for Clair to call to ProGet.
:::

Once you have created a vulnerability source, you will then need to add that vulnerability source to your container registry. You would do that by clicking _Manage Registry_ in your container registry, and clicking the _Add_ button on vulnerability sources.

After Clair has been tied to a container registry, the scheduled tasks in ProGet will automatically run, scan your images, and associate any vulnerabilities with your container image layers.  These vulnerabilities will initially be labeled as _Unassessed_.

## Vulnerability Scanning {#scanning data-title="Vulnerability Scanning"}
:::attention {.best-practice}
Clair must also have access to your local ProGet server.
:::

When ProGet is running a vulnerability scan:

{.docs} 
1. ProGet will first call to Clair to tell it what container image layers to verify
2. Clair will then call to ProGet to download the layers to test
3. ProGet will then make a second call to Clair to retrieve a list of vulnerabilities, if any, in the layers initially sent in step 1


## Assessing Vulnerabilities {#assess-vuln data-title="Assessing Vulnerabilities"}

After you've added a vulnerability source, any known vulnerabilities will be available for assessment. These are viewed by clicking the *Vulnerabilities* tab.

Select any of the vulnerabilities listed to view additional details and to assign an assessment status. Click *assess* to select a new assessment type.

:::attention {.best-practice}
Assessments can have a set expiration which will force reassessment. This ensures that compliance needs are continually addressed and that security standards are upheld as development continues.
:::
