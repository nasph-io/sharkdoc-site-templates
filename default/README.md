# SharkDoc API & Doc Portal

This repository is the base for any API Dev & Doc Portal generated and managed by SharkDoc in line with the Nasph Platform.

This website is built using [Docusaurus 2](https://docusaurus.io/), a modern static website generator.

SharkDoc will help you to generate the pages for this static site automatically.

## Preview

### Home (Dark and Light)
![Home preview](./assets/Home.png "Showing home screen.")

### Docs (Dark)
![Docs preview](./assets/Docs.png "Describe your won API.")

### Contract (Light)
![Contract preview](./assets/Contract.png "Render contract API.")

### Blog (Dark)
![Docs preview](./assets/Blog.png "Write posts.")

Intial Componets and Utilities

### Installation

```
$ yarn
```

### Local Development

```
$ yarn start
```

This command starts a local development server and opens up a browser window. Most changes are reflected live without having to restart the server.

### Build

```
$ yarn build
```

This command generates static content into the `build` directory and can be served using any static contents hosting service.

### Deployment

Using SSH:

```
$ USE_SSH=true yarn deploy
```

Not using SSH:

```
$ GIT_USER=<Your GitHub username> yarn deploy
```

If you are using GitHub pages for hosting, this command is a convenient way to build the website and push to the `gh-pages` branch.

### Initial Configuration 

For changing the initial things in this portal, you have to take a look in this file: ```docusaurus.config.js ```: 

Here you have some key configurations to do that will impact the site initially : 

```js
const config = {
  title: 'My Site', //this is the devportal title (showing on browser title on the top )
  tagline: 'Dinosaurs are cool', 
  url: 'https://your-docusaurus-test-site.com',
  baseUrl: '/',
  onBrokenLinks: 'throw',
  onBrokenMarkdownLinks: 'warn',
  favicon: 'img/favicon.ico', //change this for a new icon
  organizationName: 'facebook', // Usually your GitHub org/user name.
  projectName: 'docusaurus', // Usually your repo name.
```
![Customizing theme](./static/img/assets/toptitle.jpg)

### Starting customize visual assets

On the same file, we have another section that is reponsible for visual assets :

```js
  themeConfig:
    /** @type {import('@docusaurus/preset-classic').ThemeConfig} */
    ({
      navbar: {
        title: 'My Site', //Page title on the left on the top 
        logo: {
          alt: 'My Site Logo', 
          src: 'img/logo.svg',//this is located in ./static/src/img 
        },
  
  ...
```

## Customizing the Navbars

Here are the main points : 

```js

themeConfig:
  /** @type {import('@docusaurus/preset-classic').ThemeConfig} */
  ({
...
    items: [
      {
        type: 'doc', //this is the doc template
        docId: 'intro', //first item from the top menu
        position: 'left', // on the left
        label: 'Tutorial', // top menu
      },
      {to: '/blog', label: 'Blog', position: 'left'}, // on the left as well 
      {
        href: 'https://github.com/facebook/docusaurus',
        label: 'GitHub',
        position: 'right', //the link on the right on the top, could be you company website
      },
    ],
  },
... 
  ),     
```

![Customizing theme](./static/img/assets/top-site-left.jpg)

### Customizing the Footer 

Here are the properties that you have to tweak in order to adapt the information according your needs:

```js
      footer: {
        style: 'dark',
        links: [
          {
            title: 'Docs',
            items: [
              {
                label: 'Tutorial',
                to: '/docs/intro',
              },
            ],
          },
          {
            title: 'Community',
            items: [
              {
                label: 'Stack Overflow',
                href: 'https://stackoverflow.com/questions/tagged/docusaurus',
              },
              {
                label: 'Discord',
                href: 'https://discordapp.com/invite/docusaurus',
              },
              {
                label: 'Twitter',
                href: 'https://twitter.com/docusaurus',
              },
            ],
          },
          {
            title: 'More',
            items: [
              {
                label: 'Blog',
                to: '/blog',
              },
              {
                label: 'GitHub',
                href: 'https://github.com/facebook/docusaurus',
              },
            ],
          },
        ],
```
The css class : dark for footer (```footer-dark```), is located in the folder ```./src/css/custom.css```, in the following portion: 

```css
.footer--dark{
  background: rgb(2,0,36);
  background: 
    linear-gradient(90deg,  rgba(2,0,36,1) 0%, 
    rgba(171,24,118,1) 49%, 
    rgba(0,255,222,1) 100%);
  display: block;
}
```

![Customizing theme](./static/img/assets/footer.jpg)

### Customizing Main Themes colors

We have to main themes: 

- Default : White
- Dark : Alternative Dark theme.

In order to customize it, there is a nice utility provided by Docussaurus here: https://docusaurus.io/docs/styling-layout . It helps you to define the new colors: 

![Customizing theme](./static/img/assets/pallete.jpg)


## Changing the Redoc UI Color

Check the docussaurus.config.js the following portion:

```js

theme: {
          /**
           * Highlight color for docs
           */
          primaryColor: '#a32492',
          fontFamily: 'Ubuntu Mono, monospace',
          fontSize: '11px',
          /**
           * Options to pass to redoc
           * @see https://github.com/redocly/redoc#redoc-options-object
           */
          redocOptions: { hideDownloadButton: false, disableSearch: false },
        },
      },
    ],

```

## Developers Creating the Docs by themselves

The Developer might be interested in to create the API's Documentation just using the Markdown files, in order to do this, just add the Markdown file with the proper content, add the Swagger references and so on. 

If the documentation is made by a Documetation or business's team you can just use the Shardoc CLI, as documented bellow.

## Sharkdoc CLI

Sharkdoc CLI is a console application that aims to help api developers to create their api's docs reading the metada information from Nasph Govcenter, which is an application built on top of well known Strapi framework.

### Installing Sharkdoc 

In order to install SharkDoc, please, make sure you have the following pre-requirements: 

* Node at least version 16 or above, we strongly recommend you to use the NVM utility from the Node ecosystem .
* Yarn - v1.22 or above
* npm - at least 8 or above

Please, in order to install the Sharkdoc CLI, execute the following command: 

In order to install SharkDoc, please, make sure you have the following pre-requirements: 

```terminal
$ npm install -g sharkdoc
```

_Make sure that if you your user don't have enough permission in Linux or Mac, please, use the sudo before the npm_

Into your SharkDoc's project, this one that you did you just did the git clone, please execute the following command in the terminal:

```terminal
$ sharkdoc
```

It will execute the SharkDoc:

![image](https://user-images.githubusercontent.com/25833/160222302-0882fde2-20e7-415e-b912-392c26b7477d.png)

Select the option : Create a local api's doc page and press enter. 

If it is the first time that you are executing the SharDoc CLI, it will complain that you have not created a sharkdoc.json which is responsible to give the properties information in order to let the CLI to interact with Govcenter. Please, once it is created for you, make sure you have filled the properties approprietelly, in the follow snippet, you can see an example of this file:

```json
{  
  "govcenter_base_uri": "https://govcenter.cloud.skalena.com.br", (govcenter uri)
  "api_key": "API KEY", (the api key provided by the Govcenter, that will let the cli to interact with the matedata's API) 
  "doc_path": "/docs/apis", (default location for the api's markdown files)
  "docproject": "./"
}
 ```

It will help you to create your markdown file, you just have to inform the api key (attribute) from the Governace Center, which will be use to search the API that is respectively represented by this api key, once the information is found via the api key, when you do inform the markdown page name, everything will be created for you.

Enjoy!
