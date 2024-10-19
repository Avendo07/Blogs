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
Hugo is a framework that uses the standard HTML, CSS, JS along with golang to build static websites.

### First Interaction
You can get started here.

### My Setup
This is the setup that I chose to go with. You can check the repository here

## Deploying the website
If you have reached this part, I am assuming you have the build ready.

### Github Pages
Pages
