---
title: "White Labeling (ISV Edition)"
order: 8
---

White Labeling allows you to customize the look and feel of ProGet to create a customer-facing, branded download center. 
 
:::(Info)
This feature is intended for [ProGet ISV Edition](https://inedo.com/proget/isv); we do not support UI customization for other editions or use cases at this time.
:::


## White Label ProGet
To white label ProGet, navigate to Administration > White Label. From there, you can customize the HTML, CSS, and JavaScript to create a branded experience for your users. 

:::(Info) (💡 TIP: Use Browser Tools & Keep in  Touch)
One of the best ways to customize ProGet is with your browser's dev tools (F12) to find the CSS classes and styles you would like to override. If you find yourself needing a complicated selector or would like additional CSS classes to override, just contact us [via the support channel](https://inedo.com/support). It's usually trivial for us to add necessary classes or make suggestions on how to override the controls.
:::


### General Settings

- **Product Description:** This will update the footer text in the lower left corner.  This should traditionally include (Powered by ProGet ISV Edition).
- **Logo URL:** This is the logo image in the upper left corner and it will be resized to 35px X 35px by default in the CSS.
- **Navigation links:** This will overrite the links at the top of the page.  Each link should be in the format of `<li><a href="">My Link</a></li>`

### Login Page Settings
- **Logo URL:** This will replace the image on login page and traditionally 50px tall.
- **Additional HTML:** This will be inserted directly after the logo on the login page.

### Custom Javascript/CSS
- **Additional &lt;head&gt; Content:** This will be inserted directly in the head of the page.  You can use either inline CSS or JavaScript using `<style>` and `<script>` tags or you can link directly to CSS and JS files.
- **DarkMode-only &lt;head&gt; Content:** This will be added directly after the additional head content.  *This will only be added for users that have enabled Dark Mode in their user settings.*

### Home Page Settings
- **Title:** The title to use on the home page.
-   **Home Page HTML:** Add addtional or replace the HTML, CSS, and JavaScript to the home page.
    - **Add Additonal HTML**: This will be inserted on the home page directly after the information boxes at the top of the page.  This allows you to customize the existing homepage using HTML, CSS, and JavaScript.        
    - **Replace HTML:** This will replace the contents of the home page allowing you to make a custom home page.

## Common Styles
Their are many recurring styles through out ProGet that allow you to customize its's look and feel.  The best ways to customize ProGet is by using your browser's dev tools (F12) to find the CSS classes and styles you would like to override, but the following are some common CSS classes you may want to customize. Many pages have their own unique CSS class added to their `.content-container > .content` element. Along with this, if needed you can use custom javascript to look at a location and inject CSS classes on the page as well.

:::(Warning) (⚠ TIP: Keep It Simple)
Don't  make your customizations too complex, as it may become difficult to maintain through upgrades. Just contact us [via the support channel](https://inedo.com/support) so we can tweak the UI to make it easier for you to maintain.
:::

Some common theme classes:
- All buttons are styled with a `button` class (e.g. `<a href="/my-link" class="button solid blue">Click Here</a>`).  Then the buttons can be styled with a class named `solid` for solid buttons and `hollow` for hollow buttons.  The buttons are then styled with the follow color classes:
    - `blue` (default)
    - `grey`
    - `green`
    - `orange`
    - `red`
    - `purple`
    - `light-grey`
- `content` is a class used to enforce the body width
-  Titled Containers:
   ```html
   <div class="titled-container">
       <h2>MY Title</h2>
       <div class="controles">Controls to the right of the header</div>
       My content here
   </div>
   ```
- Tables:
   ```html
   <table class="styled-table">
      <thead>
         <tr>
            <th>Column Label</th>
         </tr>     
      </thead>
      <tbody>
         <tr>
            <td>Row columne</td>
         </tr>
   <table>
   ```
- Two Column Containers:
   ```html
   <div class="two-column-container">
      <div>Left Content</div>
      <div>Right Content</div>
   </div>
   ```
- Info Boxes (`info`, `warning`, `success`, and `error`)
   ```html
   <div class="info-box info">
      <h2>Optional Header</h2>
      <p>Content</p>
   </div>
   ```

## Home Page Styles
The home page has multiple controls you may want to use.  Each of these controls are broken up into their own classes.  The 5 main controls' CSS classes are:
- `homepage-feeds`: The list of feeds in ProGet.
- `homepage-latest-packages`: The list of the latest local packages.
- `homepage-feed-disk-space`: The Feed Disk Usage pie chart.
- `homepage-help`: The ProGet Help & Support box.
- `homepage-news`: Latest News from Inedo

## Example: ProGet's Custom Home Page
This is the custom home page code used on [https://proget.inedo.com](https://proget.inedo.com).
```html
<style>
    .downloads-table a.external {
        background-image: url(/resources/images/icons/external.svg);
        background-position: center right;
        background-repeat: no-repeat;
        padding-right: 15px;
    }

    .downloads-table p a {
        vertical-align: super;
    }

    .downloads-table .icon {
        width: 24px;
        height: 24px;
    }

    .download-box-group .download-box {
        border: 1px solid #35394b;
        margin: 20px 25px;
        display: block;
        color: #444;
        font-size: 18px;
        border-radius: 5px;
        min-width: 300px;
        max-width: 450px;
    }
    
   .download-box-group .download-box:hover {
        box-shadow: 1px 1px 4px #35394b;
        transition: opacity ease 250ms;
        text-decoration: none;
        color: #444;
    }

    .download-box-group .download-box:hover div {
        background-color: #282b3a;
    }

    .download-box-group .download-box img {
        display: block;
        margin: 20px auto;
        height: 50px;
    }

    .download-box-group .download-box p {
        margin: 30px 10px;
        line-height: 1.3em;
    }

    .download-box-group .download-box div {
        background-color: #444;
        color: #ffffff;
        font-size: 30px;
        text-align: center;
    }

    .other-downloads {
        margin-top: 40px;
    }
</style>

<div>
    <p style="font-size: small; font-style: italic;">
        This web portal was built with <u title="coming soon">ProGet ISV Edition</u>, and we used the <u title="coming soon">White label Configuration</u> feature to change ProGet's default logo, header navigation links, and footer text. We also customized the log in and the home page (i.e. this page). If you're interested in using ProGet as your public download center, <a href="https://inedo.com/contact">let us know</a>!
    </p>
</div>
<div class="titled-container">
    <h2>Most Popular Downloads</h2>
    <p style="font-size:large">
        If you're looking to install ProGet, BuildMaster, or Otter, we strongly recommend using the <a href="https://docs.inedo.com/docs/desktophub-overview" target="_blank">Inedo Hub (Windows)</a> or <a href="https://docs.inedo.com/docs/installation-linux-docker-guide" target="_blank">Docker image (Linux)</a>. Neither of those require accessing the files on this portal.
    </p>
</div>

<div class="two-column-container download-box-group">
    <div><a class="download-box" href="https://cdn.inedo.com/downloads/desktophub/InedoHubInstaller.exe"><img alt="" src="https://my.inedo.com/resources/images/icons/hub.svg"><p>The Inedo Hub is the easiest way to install ProGet, BuildMaster, or Otter on Windows, and acts as the one-stop shop for downloading, installing, upgrading/downgrading.</p><div>Download Inedo Hub</div></a></div>
    <div><a class="download-box" href="https://cdn.inedo.com/downloads/inedo-agent/InedoAgentSetup.exe"><img alt="" src="https://my.inedo.com/resources/images/icons/agent.svg"><p>The Inedo Agent is used in conjunction with BuildMaster and Otter to automate and orchestrate your infrastructure by deploying files and sending commands to your servers.</p><div>Download Inedo Agent</div></a></div>
</div>

<div class="two-column-container bottom-content">
    <div>
        <div class="titled-container">
            <h2>Product Downloads</h2>
            <p style="font-size:smaller;font-style:italic">Manually install product packages or find container images for ProGet, BuildMaster, and Otter.</p>
            <table class="styled-table">
                <tbody>
                    <tr>
                        <td><span><span class="feed-icon proget"></span><a href="/feeds/Products">Product Packages</a></span></td>
                    </tr>
                    <tr>
                        <td><span><span class="feed-icon proget"></span><a href="/feeds/PrereleaseProducts">Product Packages (Prerelease)</a></span></td>
                    </tr>
                    <tr>
                        <td><span><span class="feed-icon docker"></span><a href="/containers/registry?feedId=8">Product Container (Docker) Images</a></span></td>
                    </tr>
                </tbody>
            </table>
        </div>
    </div>
    <div>
        <div class="titled-container">
            <h2>Extension (Plug-in) Downloads</h2>
            <p style="font-size:smaller;font-style:italic">Download extensions for ProGet, BuildMaster, and Otter for manual installation.</p>
            <table class="styled-table">
                <tbody>
                    <tr>
                        <td><span><span class="feed-icon proget"></span><a href="/feeds/Extensions">Extensions</a></span></td>
                    </tr>
                    <tr>
                        <td><span><span class="feed-icon proget"></span><a href="/feeds/PrereleaseExtensions">Extensions (Prerelease)</a></span></td>
                    </tr>
                </tbody>
            </table>
        </div>
    </div>
</div>
```

## Using ProGet Asset Directories
ProGet Asset Directories are a great place to host files such as logos and additional head content (such as CSS and JS). This provides an easy location to host [versioned](/docs/proget/asset-directories-file-storage/asset-versioning-automatic-retention-rules) files that can also be [cached](/docs/proget/asset-directories-file-storage/what-is-an-asset-directory#clientside-caching). However, it's important to keep caching disabled until after you finish your white labeling to ensure that any changes you make to your files will be immediately visible without any cached content causing issues.