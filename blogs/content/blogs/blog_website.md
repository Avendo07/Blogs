+++
title = 'Blog about hosting a blog website'
date = 2024-10-19T11:00:00+05:30
draft = true
summary = "Blog explaining the process of deploying a blog website on github pages using hugo"       #TODO: Change summary
tags = ["Github Pages", "Hugo", "Website"]
+++

## How it started?
I started with writing a blog and as always it steered into something else, I started looking to host my own blogging website.
This then lead me to a rabbit hole of looking for ways to host a website. Looked at multiple blogs, a lot of them suggested hosting static websites that can be served by simply hosting the final build of your website on some cloud storage service.
This can be some cloud storage service like S3, Azure blob storage or services from other cloud providers, and enabling public access on the block storage where the website build is to be hosted.
As it turns out that Github also provides a static website hosting called [Github Pages](https://pages.github.com/). This is a free of cost service and was a perfect service available to everyone with a github account, thus I chose this.</br></br>

Now it was time to look for a framework to generate the static website. There are multiple JS frameworks which could do the trick such as NextJS or NuxtJS, but I went for Hugo as it is was the one most people suggested and had more than enough templates to get started with a basic setup done pretty quickly. Also, it's based on golang which I like, so that's a plus.

I will be dividing this blog into two parts, the first will be creating the static website using Hugo framework in the first part and the second part will be deploying the website on Github Pages. 
So, if you wish to use some other framework, you can go that route and skip to the [next part](#deploying-the-website).

## Creating the website
### What is Hugo?
Hugo is a fast, open-source static site generator ideal for building lightweight blogs. Unlike traditional CMS platforms that rely on dynamic content generation, Hugo creates static HTML files, resulting in faster performance, enhanced security, and easier maintenance. 
It supports writing content in Markdown, making blogging simple and distraction-free. With numerous themes available, you can quickly set up your site or customize it to suit your style. 
Since Hugo generates static files, there’s no need for databases or backend infrastructure, allowing you to host your blog on platforms like GitHub Pages, Netlify, or Vercel for free. The static nature of Hugo also ensures SEO-friendliness and minimizes security risks. Whether you’re a beginner or an experienced developer, Hugo offers a perfect blend of speed, flexibility, and simplicity for creating and deploying beautiful, high-performance blogs.

### First Interaction
You can get started here.</br> 
So for starting off with hugo create a new static website using the command
```bash
hugo new site <site-name>
```
This should generate a basic hugo project for you which can now be modified to get it up to your taste.
Hugo has themes which have a predefined configuration which allow you to get started very quickly without having to write any kind of web application code. 
These would contain HTML, CSS, JS code along with the routing for the website and other configuration.

### Themes(pending)
How they work?
Themes are a set of preconfigured hugo sites making hugo easy to pick up and get it working. These would contain the HTML, CSS and JS files for the webpages, default routing for the pages.
These themes can be are also configurable using the config.yaml/toml files by modifying the properties exposed by these themes.
These themes can be added to a project generally in two ways
`hugo theme add`
Using submodules


### Hugo site configuration
The config.yaml file in Hugo is where you define your site’s main settings and configurations. Here’s a quick overview of commonly used sections:
```yaml
baseURL: "https://example.com"          # Your site URL
languageCode: "en-us"                    # Site language
title: "My Blog"                         # Site title
theme: "your-theme"                      # Theme directory name
summaryLength: 70                        # Length of post summaries

permalinks:
  post: "/:year/:month/:title/"          # URL structure for posts

params:                                  # Custom parameters for your site
  description: "A blog about tech"       # Site description
  author: "Your Name"                    # Author name for posts

markup:                                   # Markdown configuration for rich formatting
  highlight:
    style: "monokai"                      # Code highlight theme
    lineNos: true                         # Enable line numbers
```
This file can be adjusted to control the look, feel, and functionality of your Hugo site, from theme settings to URL structures and custom parameters. 
It’s written in YAML, making it easy to read and modify, but can also be written in toml or yaml. For larger sites, you can add sections for multilingual support, social media links, and more advanced configurations as needed.
You can refer to the [documentation](https://gohugo.io/getting-started/configuration/) for more details for more complex implementations.

### Build Pipeline[^1]
We can have a build pipeline to generate static website code from the hugo code that can be hosted. The following snippet of code is written for a Github Actions pipeline
```yaml
jobs:
  # Build job
  build:
    runs-on: ubuntu-latest
    env:
      HUGO_VERSION: 0.134.2
    steps:
      - name: Install Hugo CLI
        run: |
          wget -O ${{ runner.temp }}/hugo.deb https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb \
          && sudo dpkg -i ${{ runner.temp }}/hugo.deb
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: recursive
          sparse-checkout: |
            blogs
      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v5
      - name: Install Node.js dependencies
        run: "[[ -f package-lock.json || -f npm-shrinkwrap.json ]] && npm ci || true"
      - name: Build with Hugo
        working-directory: ./blogs
        env:
          HUGO_CACHEDIR: ${{ runner.temp }}/hugo_cache
          HUGO_ENVIRONMENT: production
          TZ: America/Los_Angeles
        run: |
          hugo \
            --gc \
            --minify \
            --baseURL "${{ steps.pages.outputs.base_url }}/"
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: |
            ./blogs/public
            ./blogs/resources
```

Let's run through the steps used to build the code.
- The first step installs hugo package on the github runner using a debian package.
- Then we do a sparse checkout of the repository in the blogs folder with submodules included, this would download the themes installed for the site.
- The next step enables github pages and retrieves metadata from the github pages that can be used in the workflow
- We install any dependencies that are required by the site
- Then the hugo cli is used to build the hugo site and output it to the `public` and `resources` directories 
- The last step creates an artifact and uploads it to github and is accessible by the workflow

### Output
What are the generated outputs

## Deploying the website
If you have reached this part, I am assuming you have the build of the website ready to be deployed. Now, to host the website I used Github Pages to host the website. The idea here is to host the code on a github repository and to deploy changes to the repository after each commit using a github actions pipeline. 

### Github Pages
GitHub Pages is a free web hosting service from GitHub, perfect for hosting static websites such as personal portfolios, project documentation, or blogs. It serves HTML, CSS, and JavaScript files directly from a GitHub repository, making it ideal for lightweight, client-side content. Any changes pushed to the repository trigger automatic deployments, ensuring your site stays up to date without manual intervention.

Setting up a GitHub Pages site is straightforward: create a repository, push your website files, and enable GitHub Pages from the repository settings. The site becomes accessible through `<username>.github.io` or a custom domain, with HTTPS enforced for security. GitHub Pages is popular for hosting personal websites, open-source documentation, event pages, and tech blogs, offering a seamless way to publish and maintain content with minimal configuration.

### Deploying(pending) 
Now that we have the static website code to be hosted on github pages. We need to enable github pages feature for the repository. 
Steps to enable Github Pages:
- Go to the concerned github repository, and go to the settings tab
- Go to the pages section and enable pages section

[//]: # (- ...steps  #TODO)
- You can also host this website on custom domains by entering the domain name and dns a-records in the required section
This should enable github pages and the website should be hosted by default on `<username>.github.io/<repository-name>` or the custom domain if added

### Deployment Pipeline[^1]
We can have a deployment pipeline as follows to deploy the build from the artifact to github pages and is accessible publicly, similar to the following snippet
```yaml
jobs:
  build:
    # At a minimum this job should upload artifacts using actions/upload-pages-artifact
  deploy:
    permissions:
      pages: write
      id-token: write
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build                                      #There is supposed to be a build step before this that uploads an artifact that is hosted on the github pages
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```
The workflow here uses the deploy-pages github action to deploy the artifact uploaded by the `build` step in the workflow. You can have a look at different details about the action in the [documentation](https://github.com/marketplace/actions/deploy-github-pages-site) for more details.

## The final workflow(pending)
Now that we have the deployment pipeline setup to deploy and host the website on Github Pages, let's discuss how the workflow looks like after this initial setup.
For adding a new blog to the site, we need to create a new markdown file in the `content/blogs`(specific to the current setup) directory, this would add a blog to the blogs section of the website.
The blogs can be tagged with frontmatter metadata such as publishing date and expiration date so that blogs can be published and unpublished whenever required.

[//]: # (Add a link for front matter here #TODO)
[//]: # (These will only be analysed when a new build is created though, if this is supposed to be analysed daily, set the pipeline to run every night)
After we make a commit to the repo and push it to the VCS(Github here), the pipeline should get triggered and create a build of the current content and add it to the outputs directory. The contents of the outputs directory is published on the website.

### My Setup (pending)
This is the setup that I chose to go with.

Theme -> URL
Config -> config.yaml changes
Image addition
Adding a new blog

You can check the repository here

### The theme fork(pending)
Fork of the repository for any changes that I make

## How this helps you?
- You can maintain some basic notes in markdown format that you can simply push to a repository to get it posted on the website.
- You could do this to publish the whole host of notes that you might have already maintained
- Very low time to start, without any web development experience required
- Multiple themes already available to choose from [here](https://themes.gohugo.io/)


[^1]: The complete workflow can be found [here](https://github.com/Avendo07/Blogs/blob/main/.github/workflows/hugo.yaml)