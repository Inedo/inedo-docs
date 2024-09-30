---
title: "HOWTO: Proxy RubyGems from RubyGems.org"
order: 1
---

ProGet lets you create ["Feeds"](/docs/proget/feeds/feed-overview) that can be configured to proxy Ruby Gems from [RubyGems.org](https://rubygems.org/), the Ruby community's gem hosting service. This will let teams install gems directly or add as a source in a project's `Gemfile`.  

The advantages of using ProGet as a source to proxy gems are:
* ProGet will cache gems, allowing teams to access them even when RubyGems.org is down
* ProGet will show which gems are being downloaded and used frequently

In this article, we'll explain how to create a feed in ProGet that will proxy packages from RubyGems.org and then configure it as a source in your local Ruby environment, or add it as a source in a `Gemfile`. 

We’ll also explain how to set up a private source for using internal gems, as well as how to establish a package approval process to manage which packages your team can utilize in production.

## Step 1: Create and Name a RubyGems Feed { #step-1 }

First we will create a RubyGems feed that will proxy packages from [RubyGems.org](https://rubygems.org/). You can read more on creating feeds by reading [Creating and Managing Feeds](/docs/proget/feeds/feed-overview#creating-and-managing-feeds)

In your ProGet instance, select "Feeds" and "Create New Feed", and select "RubyGems".

![](){height="" width="50%"}

Then select "Connect to RubyGems.org" which will allow us to proxy and cache packages from the RubyGems.org.

![](){height="" width="50%"}

Select "No, Create One Feed", as we will only need a single feed to proxy gems. Now we'll name the feed, which we will call `public-gems`, and then click "Create Feed".

![](){height="" width="50%"}

We are then presented with several options. More information on these can be found in the [Vulnerability Scanning and Blocking](/docs/proget/sca/vulnerabilities) documentation.

![](){height="" width="50%"}

Then select [Set Feed Features]. ProGet will create The `public-gems` feed and redirect you to it. This feed will now be populated with packages proxied from RubyGems.org

![](){height="" width="50%"}

## Step 3: Adding the Feed to Your Local Ruby Environments { #step-3 }

To install packages from the `public-gems` feed, you will first need the feed's URL. This can be found on the top right of the feed page:

![](){height="" width="50%"}

Then, you will need to add it as a source by entering:

```bash
$ gem sources --add «feed-url»
```

For example, to add your `public-gems` feed on your ProGet instance hosted on `http://proget.corp.local` you would enter:

```bash
$ gem sources --add http://proget.corp.local/rubygems/public-gems/
```

By default, RubyGems.org is configured as a source. We recommend removing this so that packages are exclusively installed from the `public-gems` feed. To remove RubyGems.org as a source, enter:

```bash
$ gem sources --remove https://rubygems.org/
```

#### Adding The Feed as a Source in Ruby/Rails Projects

Whle using `gem sources --add` adds a gem source globally for all Ruby and/or Ruby on Rails projects, adding a source in a `Gemfile` specifies the `public-gems` feed for a specific project. This makes sure bundler installs dependencies only from this feed, which helps maintain a consistent project environment. To add `public-gems` as a project's source, add the following to the `Gemfile`:

```ruby
source 'http://proget.corp.local/rubygems/public-gems/'

gem 'bundler'
```

## Step 4: (Optional) Confirming Connection to your RubyGems Feed

You can confirm that your local Ruby environment is configured with your RubyGems feed by entering:

```bash
$ gem sources list 
```

Which should return:

```bash
*** CURRENT SOURCES ***

http://proget.corp.local/rubygems/public-gems/
```

You can also list the packages in the source by entering:

```bash
$ gem search --remote
```

## (Optional) Authenticating to Your RubyGems Feed

By default your `public-gems` feed will not require authentication and can be viewed anonymously. However, you may want to make your feed private and configure it to require authentication to access. One reason for doing this would be when using internal gems in a feed, either solely or in addition to using gems from RubyGems.org. 

First navigate "Settings"> "Manage Security" and select the "Tasks/Permissions" tab. Remove anonymous access by clicking the small "X" in the "Anonymous" entry. 

![](/resources/docs/proget-permissions-remove.png){height="" width="50%"}

Next, you will need to create an [API Key](/docs/proget/reference-api/proget-apikeys). 

Navigate to "Administration Overview" > "API Keys & Access Logs" under "Security & Authentication"

![](/resources/docs/proget-admin-apikeys.png){height="" width="50%"}

Then select "Create API Key".

![](/resources/docs/proget-apikey-new.png){height="" width="50%"}

Fill in the fields by selecting "Feeds ("Use Certain Feeds)" as the "Feed Type" and selecting the `public-gems` feed. Then set the API key. You can specify any alphanumeric sequence for this, or leave it blank to autogenerate one.

Make sure that the "View/Download" box is checked, and then select "Save".

![](){height="" width="50%"}

Now, we'll add the feed to a local Ruby environment which will require the URL from [Step 3](#step-3), as well as your API key. Add the feed as a source by entering:

```bash
$ gem sources --add http://api:«api-key»@«feed-url
```

For example, authenticating to `http://proget.corp.local/rubygems/public-gems/` using the API key `abc12345` you would enter:

```bash
$ gem sources --add http://api:abc12345@proget.corp.local/rubygems/public-gems/
```

Confirm that it was set by entering:

```bash
$ gem sources list 
```

#### Authenticating from a Project's Gemfile

When authenticating to your `public-gems` feed from a project's `Gemfile` you will need to add the following line:

```ruby
source 'http://api:abc12345@proget.corp.local/rubygems/public-gems/'
```

## (Optional) Creating a Package Approval Flow

In this article, we talked about how to proxy packages from [RubyGems.org](https://rubygems.org/). However, this allows developers to use any OSS package from the public source without oversight. In many cases, it's important to include some form of approval or oversight in development or production, which can be done by introducing a ["Package Approval Flow"](/docs/proget/packages/package-promotion).

To set up a package approval flow, refer to [HOWTO: Approve and Promote Open-source Packages](/docs/proget/packages/package-promotion/proget-howto-promote-packages). This guide uses NuGet feeds as an example, but the steps are identical when creating RubyGem feeds.

After creating your "Unapproved" and "Approved" feeds, follow the steps in [Step 3](#step-3) to add the "Approved" feed as a source in your local Ruby environments, entering:

```bash
$ gem sources --add «feed-url»
```

Or in a project's gemfile by adding the line:


And optionally confirming the connection by entering:

```bash
$ gem sources list 
```