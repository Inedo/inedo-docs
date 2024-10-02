---
title: "HOWTO: Create and Push RubyGems to a Private Source in ProGet"
order: 2
---

ProGet let's you set up ["Feeds"](/docs/proget/feeds/feed-overview) as private sources for you to push internal RubyGems to. This will let you  store and share them within your organization.

This guide will walk you through the process of setting up a RubyGems feed as a private source in ProGet. We'll also talk about building, uploading, and installing RubyGems from this source.

## Step 1: Create and Name a RubyGems Feed

Start by creating a RubyGems feed to host your RubyGems. Navigate to "Feeds" and "Create New Feed". Then select "RubyGems".

![](){height="" width="50%"}

Now select "No Connectors (Private packages only)" as this feed will be intended as a private repository.

![](){height="" width="50%"}

From here, we name our feed. For this example, we will call it `internal-gems`, and then click "Create Feed".

![](){height="" width="50%"}

You'll then see several options related to ProGet's [Vulnerability Scanning and Blocking](/docs/proget/sca/vulnerabilities) features. These are only for users looking to use open source gems like those hosted on [RubyGems.org](https://rubygems.org). Leave these boxes unchecked, and select [Set Feed Features]. You will then be directed to the new `internal-gems` feed, currently empty.

![](){height="" width="50%"}

## Step 2: Create an API Key { #step-2 }

Next, we'll create an [API Key](/docs/proget/reference-api/proget-apikeys) allowing our local environment to authenticate to our `internal-gems` feed. This allows us to push and install packages from the feed.

Start by navigating to "Administration Overview" > "API Keys & Access Logs" under "Security & Authentication"

![](/resources/docs/proget-admin-apikeys.png){height="" width="50%"}

Then select "Create API Key"

![](/resources/docs/proget-apikey-new.png){height="" width="50%"}

Next, fill in the fields by selecting "Feeds (Use Certain Feeds)" as the "Feed Type" and selecting the `internal-gems` feed. Then set the API key. You can use any alphanumeric sequence, or just leave it blank to autogenerate one.

![](){height="" width="50%"}

Make sure the "View/Download" and "Add/Repackage" boxes are checked, and then select "Save".

## Step 3: Build Your RubyGem

Now, we will build our RubyGem. More information on developing these can be found in [the official documentation](https://guides.rubygems.org/make-your-own-gem/).

To build your package, you'll first need `bundler` installed. To do this enter:

```bash
$ gem install bundler
```

Next, set the current working directory to the folder that contains your project files such as your `.gemspec`, `Gemfile`, and `.rb` files. Then build your package by entering:

```bash
$ gem build «gemspec-file»
```

For example, to build a gem named `MyGem` you would enter:

```bash
$ gem build my_gem.gemspec
```

Your `.gem` RubyGem is then built, and saved inside your project folder. 

### Step 4: Push Your RubyGem to ProGet { #step-4 }

To push your RubyGem to your `internal-gems` feed, you will need your feed URL. This can be located in the top right of the feed's page:

![](){height="" width="50%"}

Then, enter the following:

```bash
$ gem push gem-file» --host «feed-url»
```

For example, to push your `my_gem-1.0.0` RubyGem to your `internal-gem`s feed you would enter:

```bash
$ gem push my_gem-1.0.0 --host http://proget.corp.local/rubygems/internal-gems/
```

You will be prompted for your credentials. For "Username/email" enter `api`, and for "Password" enter the API key created in [Step 2](#step-2)

If successful, you should see the message:

```
Signed in with API key: LAPTOP-3FJH82K9-Smith-20240930160735.
Pushing gem to http://proget.corp.local/rubygems/internal-gems/...
```

And the gem should be pushed to your `internal-gems` feed:

![](){height="" width="50%"}

## Step 5: Adding the Feed to Your Local Ruby Environments { #step-5 }

To install packages from the `internal-gems` feed, take the URL from [Step 4](#step-4), and add it as a source by entering:

```bash
$ gem sources --add «feed-url»
```

For example, to add your `internal-gems` feed on your ProGet instance hosted on `http://proget.corp.local` you would enter:

```bash
$ gem sources --add http://proget.corp.local/rubygems/internal-gems/
```

By default, RubyGems.org is configured as a source. This will need to be removed so that packages are exclusively installed from the `internal-gems` feed. To remove RubyGems.org as a source, enter:

```bash
$ gem sources --remove https://rubygems.org/
```

## Step 6: (Optional) Confirming Connection to your RubyGems Feed

You can confirm that your local Ruby environment is configured with your RubyGems feed by entering:

```bash
$ gem sources list 
```

Which should return:

```bash
*** CURRENT SOURCES ***

http://proget.corp.local/rubygems/internal-gems/
```

You can also list the packages in the source by entering:

```bash
$ gem search --remote
```

## (Optional) Authenticating to Your RubyGems Feed

By default your `internal-gems` feed will not require authentication and can be viewed anonymously. However, you may want to make your feed private and configure it to require authentication to access, for example, if your ProGet instance is public. To do this you can remove anonymous access and create an API key to authenticate to the feed.

Navigate "Settings"> "Manage Security" and select the "Tasks/Permissions" tab. Remove anonymous access by clicking the small "X" in the "Anonymous" entry. 

![](/resources/docs/proget-permissions-remove.png){height="" width="50%"}

Then create an API key as explained in [Step 2](#step-2), but this time make sure the "View/Download" box is checked, and then select "Save".

Now, add the `internal-gems` feed to your either your Ruby environment or `Gemfile` as we did in [Step 5](#step-5) but this time take the API key you just created in addition to the the `internal-cran` feed URL and enter:

```bash
$ gem sources --add http://api:«api-key»@«feed-url
```

For example, authenticating to `http://proget.corp.local/rubygems/internal-gems/` using the API key `abc12345` you would enter:

```bash
$ gem sources --add http://api:abc12345@proget.corp.local/rubygems/internal-gems/
```