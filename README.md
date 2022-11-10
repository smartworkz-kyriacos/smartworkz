# Hugo on GitHub Pages with Forestry and Netlify

[![Netlify Status](https://api.netlify.com/api/v1/badges/7d3e4337-98ef-4936-a41b-93a11e98b379/deploy-status)](https://app.netlify.com/sites/smartworkz/deploys)

![1667921143177](image/README/1667921143177.png)

Smartworkz's static (web) site is based on the following integrations:

- [Hugo](https://gohugo.io/) a static site generator installed on your local machine for rapid static site design and development
- [Github](https://github.com/) to store backups
- [Forestry](https://forestry.io/) a static site CMS hosted service for daily content creation/editing
- [Netlify](https://www.netlify.com/) a static file hosting serviice to watch Github for any changes and build/deploy as needed

  ![1667921475381](image/README/1667921475381.png)

## Prerequisites

* If not already done, *Install [Hugo](https://gohugo.io/getting-started/installing/)*
* If not already done, *Install [Visual Studio Code](https://code.visualstudio.com/download) (VSC)*
* If not already done, *Install [Git Bash](https://git-scm.com/downloads)*
* *(Optional) Configure Git Bash as the default terminal for VSC*

  1. Click View, Terminal
  2. After the Terminal appears, press the F1 key
  3. Type the following, Terminal: Select Default Profile
  4. Select from the dropdown, Git Bash

## Hugo and GitHub Pages

### *Navigate to your local Git repository*

![1667921557704](image/README/1667921557704.png)

### `cmd` *in your File Explorer path and press the Enter key*

![1667921581221](image/README/1667921581221.png)

The command window prompt opens:

![1667921610927](image/README/1667921610927.png)

### `code .` *in the prompt and press the Enter key*

![1667921660074](image/README/1667921660074.png)

The Visual Studio Code (VSC) application opens

* *Trust the Authors and on the Menu click View and select Terminal*

  ![1667921704176](image/README/1667921704176.png)

  The Git Bash terminal appears:

  ![1667921756630](image/README/1667921756630.png)

### Clone a Hugo Template of this site

```
This contains all the files used to generate the site, not the site itself.
Later, will create another separate repository to host the static (web) site files
```

![1667921806669](image/README/1667921806669.png)

`git clone https://github.com/smartworkz-kyriacos/smartworkz-site.git`

This clones the repository in the local git repository path

![1667921827684](image/README/1667921827684.png)

### *`cd` into site*

![1667921846623](image/README/1667921846623.png)

`cd smartworkz-site`

### *Check status*

![1667921876011](image/README/1667921876011.png)

`git status`

### *Check directory structure*

![1667921911729](image/README/1667921911729.png)

```
ls
ls -la
```

### *Check smartworkz-site tree view*![1665576788318](image/README/1665576788318.png)

### *Open and log in to your [GitHub account](https://github.com/)*

### *Create a GitHub repository for the generated static (web) site files*

![1667921927670](image/README/1667921927670.png)

The name of this repository follows the format

`<USERNAME>`.`github.io`

Replace with your own `<USERNAME>`

Return to the terminal and change to the local Git directory for your `<USERNAME>`.`github.io` hosting site `cmd` in and `code .` to open in VSC

![1667921942696](image/README/1667921942696.png)

### Clone your previous GitHub Repository

(Smartworkz's template containing all static (web) files in your  `<USERNAME>`.`github.io` local Git repository)

  `git clone https://github.com/smartworkz-kyriacos/smartworkz-site.git`

![1667921957024](image/README/1667921957024.png)

### *Check that the template site is functioning*

![1667921993316](image/README/1667921993316.png)

 `hugo server -D`

This will set up the site locally and allow it to be viewed at the address specified in the shell (http://localhost:1313). Observe the `/public` folder created:

![1667922012247](image/README/1667922012247.png)

### *Generate the static files and push it to the `<USERNAME>.github.io` repository*

1. Remove the `public` directory in case as it was created with Hugo previously

   `rm -rf public`
2. Generate the actual website files in your site project directory in a subdirectory called `public/` with a submodule repository (**note:** *below is a one-line code*)

   `git submodule add -b main https://github.com/<USERNAME>/<USERNAME>.github.io.git public`

Git submodules are  nested repositories. By making the `.github.io` repository a submodule of the `smartworkz-site` repository, we' re replicating the structure Hugo uses by making the rendered website files in a `/public/` subdirectory inside the site project directory `smartworkz-site` itself.

### *Deploy to `<USERNAME>`.`github.io` by running the following commands:*

```
hugo
cd public
git add .
git commit -m "write a commit message here to describe the changes"
git push origin main
```

### *Automate deployment by creating a bash file `deploy.sh` file*

```
#!/bin/sh

# If a command fails then the deploy stops
set -e

printf "\033[0;32mDeploying updates to GitHub...\033[0m\n"

# Build the project.
hugo # if using a theme, replace with `hugo -t <YOURTHEME>`

# Go To Public folder
cd public

# Add changes to git.
git add .

# Commit changes.
msg="rebuilding site $(date)"
if [ -n "$*" ]; then
	msg="$*"
fi
git commit -m "$msg"

# Push source and build repos.
git push origin master
```

### *Run deployment by typing:*

`./deploy.sh "write a commit message here to describe the changes you made"`

Changes will be live shortly at your  `https://<USERNAME>.github.io` with any changes everytime you deploy your GitHub Pages

## Hugo and Forestry Integration

### *Open and log in to your [Forestry account](https://forestry.io/)*

![1667922037190](image/README/1667922037190.png)

### *Click Add Site, Select Hugo as your static site generator*

![1667922061622](image/README/1667922061622.png)

### Select GitHub as your git provider

![1667922078427](image/README/1667922078427.png)

### *Choose Git repository*

![1667922093731](image/README/1667922093731.png)

GitHub Pages hosts  your website using Hugo and Forestry.io as your CMS. Any changes you make using Forestry will auto-commit to your project repository.

![1667922115287](image/README/1667922115287.png)

### *`git pull` once you're done editing your site content with Forestry.*

## Hugo and Netlify Integration

Netlify can host your Hugo site with CDN, continuous deployment, 1-click HTTPS, an admin GUI, and its own CLI.

[Netlify](https://www.netlify.com/) provides continuous deployment services, global CDN, ultra-fast DNS, atomic deploys, instant cache invalidation, one-click SSL, a browser-based interface, a CLI, and many other features for managing your Hugo website.

### Assumptions

* You have an account with GitHub, GitLab, or Bitbucket.
* You have completed the [Quick Start](https://gohugo.io/getting-started/quick-start/) or have a Hugo website you are ready to deploy and share with the world.
* You do not already have a Netlify account.

### Create a Netlify account

Go to [app.netlify.com](https://app.netlify.com/) and select your preferred signup method. This will likely be a hosted Git provider, although you also have the option to sign up with an email address.

The following examples use GitHub, but other git providers will follow a similar process.

![Screenshot of the homepage for app.netlify.com, containing links to the most popular hosted git solutions.](https://d33wubrfki0l68.cloudfront.net/0e9f9cefe968382536d4e4baa66e49945ad2694c/e20ef/images/hosting-and-deployment/hosting-on-netlify/netlify-signup.jpg)

Selecting GitHub will bring up an authorization modal for authentication. Select “Authorize application.”

![Screenshot of the authorization popup for Netlify and GitHub.](https://d33wubrfki0l68.cloudfront.net/66276caf9e5deee836ba60fab50f78f6074e3ca0/0cc43/images/hosting-and-deployment/hosting-on-netlify/netlify-first-authorize.jpg)

### Create a New Site with Continuous Deployment

You're now already a Netlify member and should be brought to your new dashboard. Select New site from git.

![Screenshot of the blank Netlify admin panel with no sites and highlighted <span class=](https://d33wubrfki0l68.cloudfront.net/1a92de85be074abc024967fa7088c8b719c32466/f7496/images/hosting-and-deployment/hosting-on-netlify/netlify-add-new-site.jpg)

Netlify will then start walking you through the steps necessary for continuous deployment. First, you’ll need to select your git provider again, but this time you are giving Netlify added permissions to your repositories.

![Screenshot of step 1 of create a new site for Netlify: selecting the git provider](https://d33wubrfki0l68.cloudfront.net/742c7be22b24a5df82a39f7cd259a04a7abdd150/db696/images/hosting-and-deployment/hosting-on-netlify/netlify-create-new-site-step-1.jpg)

And then again with the GitHub authorization modal:

![Screenshot of step 1 of create a new site for Netlify: selecting the git provider](https://d33wubrfki0l68.cloudfront.net/dd85bd12e419baeb7ef56e45c43235d2004ce341/77531/images/hosting-and-deployment/hosting-on-netlify/netlify-authorize-added-permissions.jpg)

Select the repo you want to use for continuous deployment. If you have a large number of repositories, you can filter through them in real time using repo search:

![Screenshot of step 1 of create a new site for Netlify: selecting the git provider](https://d33wubrfki0l68.cloudfront.net/188f9bfa9eb4997757414ec0ac1979d7111c9741/8f7a6/images/hosting-and-deployment/hosting-on-netlify/netlify-create-new-site-step-2.jpg)

Once selected, you’ll be brought to a screen for basic setup. Here you can select the branch you wanted published, your [build command](https://gohugo.io/getting-started/usage/#the-hugo-command), and your publish (i.e. deploy) directory. The publish directory should mirror that of what you've set in your [site configuration](https://gohugo.io/getting-started/configuration/), the default of which is `public`. The following steps assume you are publishing from the `master` branch.

### Configure Hugo Version in Netlify

You can [set Hugo version](https://www.netlify.com/blog/2017/04/11/netlify-plus-hugo-0.20-and-beyond/) for your environments in `netlify.toml` file or set `HUGO_VERSION` as a build environment variable in the Netlify console.

For development `netlify.toml`:

```toml
[build]
  publish = "public"
  command = "hugo --gc --minify"
  
[build.environment]
  HUGO_VERSION = "0.84.1"
  HUGO_THEME = "educenter"
```

### Build and Deploy Site

In the Netlify console, selecting “Deploy site” will immediately take you to a terminal for your build:.

![Animated gif of deploying a site to Netlify, including the terminal read out for the build.](https://d33wubrfki0l68.cloudfront.net/a9f55d92792a554cb775cd0d10eddf445338b83a/0a424/images/hosting-and-deployment/hosting-on-netlify/netlify-deploying-site.gif)

Once the build is finished—this should only take a few seconds–you should now see a “Hero Card” at the top of your screen letting you know the deployment is successful. The Hero Card is the first element that you see in most pages. It allows you to see a quick summary of the page and gives access to the most common/pertinent actions and information. You’ll see that the URL is automatically generated by Netlify. You can update the URL in “Settings.”

![Screenshot of successful deploy badge at the top of a deployments screen from within the Netlify admin.](https://d33wubrfki0l68.cloudfront.net/e2ea775b0985b93f2e0d7c88ae134e90c3e7446e/8a3d7/images/hosting-and-deployment/hosting-on-netlify/netlify-deploy-published.jpg)

[Visit the live site](https://smartworkz.netlify.app/)

Now every time you push changes to your hosted git repository, Netlify will rebuild and redeploy your site.

See [this blog post](https://www.netlify.com/blog/2017/04/11/netlify-plus-hugo-0.20-and-beyond/) for more details about how Netlify handles Hugo versions.

### Next Steps

You now have a live website served over HTTPS, distributed through CDN, and configured for continuous deployment. Dig deeper into the Netlify documentation:

1. [Using a Custom Domain](https://www.netlify.com/docs/custom-domains/)
2. [Setting up HTTPS on Custom Domains](https://www.netlify.com/docs/ssl/)
3. [Redirects and Rewrite Rules](https://www.netlify.com/docs/redirects/)
