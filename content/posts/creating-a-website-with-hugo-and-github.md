---
author: Zachary Santamaria
title: Creating a Website With Hugo and Github
date: 2023-03-07T19:25:44-06:00
description: Creating your own blog page with hugo and github actions!
draft: false
tags: [hugo, github-actions]
categories: [Guides] # Will change to Tech eventually
comment : false
hidden: false
---

# Introduction
Have you ever wondered, man I wish I could get a job. Well now you can! This post will show someone how to create a website using hugo and have it hosted with github actions. Once you have a blog page, you can start posting about all the cool things you learn in tech. This can, not only, make you more knowledgeable in the field but, it also could help someone who needs a guide! 


# Why use Github?
**ITS FREE**!  
Also, it's the most common remote code repository so, nearly everyone has an account. Leading to more familiarity with the project and the tools github provides. Not that you'll need money anyways once your interviewer finds your blog page and sees your work.     

# Why use hugo?
There are many reason to use hugo is because its impressive organization they use in order to maintain blog posts. Let's say you're lazy, which is more than likely true if you are using this post, this is where hugo's features shine. Creating post are easy with the use of a simple command, which we will explore later. Also, you can easily maintain posts and easily add a theme so you can just worry about posting.

# Steps
## Requirements
- hugo
- github accounts

## Installation Requirements
The first step is to get **hugo** and `git` installed. You can skip to the next section, if you already have those applications installed. Now normally I do not mind showing y'all how to install these applications. However, they have great documentation so it seems redundant to show they same steps they have already documented. Instead I will redirect you to those instructions and you can comeback when that portion is completed. Here are the links for installation [Hugo](https://gohugo.io/installation/) and [git](https://github.com/git-guides/install-git) 

## Creating a repository on github
Hopefully, the installation of the requirements wasn't too bad. Now we will begin with creating a repository on github. First navigate to your homepage on github. In my case, it would be `https://github.com/ZacharySantamaria` or in your case `https://github.com/<username>`. Next on the upper-right corner there is a "+" icon. Select that a press `new repository`. This is the important part to this step, you will need to name the repository based on your username. For instance, I will have to name the repository `zacharysantamaria.github.io`. In your case, it will be `<username>.github.io`. I would normally explain why we MUST have this naming convention however, github has documentation [here](https://docs.github.com/en/pages/getting-started-with-github-pages/about-github-pages) that explains it. From here, you can add a description and anything else that you would like. Once you are finished, select `create repository` at the bottom.

## Creating the hugo project
> Before you begin this step, you will need to have `hugo` installed. 

Now you will navigate locally where you would like to create your project. In my case, I will be placing the project in this directory `~/Zachary/Projects`. Once I am there, I will need to enter the command 
```bash
hugo new <name-of-project>
```
> `<name-of-project>` can be your choice of name. once that is created we will now need to link the remote repo to the local. 

## Connecting github with local hugo project
From here you will need to connect all your local changes to the remote repo on github. to do this you will need to be in the newly created hugo directory. In my case, it would be `~/Zachary/Projects/Zachary-Blog`. Then you will need to run the following command 
```bash
git remote add origin git@github.com:ZacharySantamaria/testing.git
git branch -M main
git push -u origin main
```
If everything worked out, it will not shout errors at you! 

## Creating github action to post our website
Okay, from here we will need to create a file to specify what we want our github actions to do. You will navigate to your hugo directory. Next you will need to create a file within your `.git` directory. Since this folder has a period in front of it, it may not allow you to view it from your IDE. I suggest using a terminal then typing
```bash
ls -al
```
To view everything within a directory. Now, once inside that folder you will need to create a directory called workflows then change into that directory. To recap, you should be in the `~/<path-to-blog-code>/.git/workflows`. Next, you can create a file called `hugo.yml`. In that file you will place this content which will tell github how to compile and host your website!
```yaml
# Sample workflow for building and deploying a Hugo site to GitHub Pages
name: Deploy Hugo site to Pages

on:
  # Runs on pushes targeting the default branch
  push:
    branches: ["main"]

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# Sets permissions of the GITHUB_TOKEN to allow deployment to GitHub Pages
permissions:
  contents: read
  pages: write
  id-token: write

# Allow one concurrent deployment
concurrency:
  group: "pages"
  cancel-in-progress: true

# Default to bash
defaults:
  run:
    shell: bash

jobs:
  # Build job
  build:
    runs-on: ubuntu-latest
    env:
      HUGO_VERSION: 0.108.0
    steps:
      - name: Install Hugo CLI
        run: |
          wget -O ${{ runner.temp }}/hugo.deb https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb \
          && sudo dpkg -i ${{ runner.temp }}/hugo.deb
      - name: Install Dart Sass Embedded
        run: sudo snap install dart-sass-embedded
      - name: Checkout
        uses: actions/checkout@v3
        with:
          submodules: recursive
      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v3
      - name: Install Node.js dependencies
        run: "[[ -f package-lock.json || -f npm-shrinkwrap.json ]] && npm ci || true"
      - name: Build with Hugo
        env:
          # For maximum backward compatibility with Hugo modules
          HUGO_ENVIRONMENT: production
          HUGO_ENV: production
        run: |
          hugo \
            --minify \
            --baseURL "${{ steps.pages.outputs.base_url }}/"
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v1
        with:
          path: ./public

  # Deployment job
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v1
```
Okay, we are done with everything involving github setup. I know it was very painful, but we are almost done! This part is the more fun part of the guide. Let's pick a theme!

## Setting up a hugo theme
You can explore and find a theme on the hugo website [here](https://themes.gohugo.io). I am deciding to use a theme called `diary` so I will continue this  guide using that theme. Hopefully, you haven't left your project directory. I am usually assuming you complete a section of this guide then eat lunch or something. Anyways, I decided to add a theme as a git-module. Although, I believe is the only way to download this particular theme however, some allow you to add as a hugo-module which is a bit different so please refer to those docs. Alright, you're going the run a few commands to download a theme.
```bash
git submodule add https://github.com/AmazingRise/hugo-theme-diary.git themes/diary
git submodule update --remote --merge
```

# Congratulations! You have finished!
Now you can create blog posts in the `content/posts` directory. Thankfully there is a command that allows you to create posts. **Remember** this command! You will use it often, if you are going to use your website!
```bash
hugo new posts/my-first-post.md
```
If you would like to customize the theme more I suggest looking at their documentation to see what options your theme could have. From here, you can create blog posts with markdown! If you have any experience with it, you'll know you can write quick documentation very fast! Now from here, you can start documenting your process programming and become the software engineer that you've always wanted to be. Good luck and remember I am always looking for more coffee.