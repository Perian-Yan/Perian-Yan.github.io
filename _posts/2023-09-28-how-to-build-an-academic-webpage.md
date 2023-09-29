---
layout: post
title:  How to Build an Academic Webpage
date:   2023-09-28
description: setup Jekyll theme al-folio on Windows
tags: tools
categories: 
---
I use the Jekyll theme: [al-folio](https://github.com/alshedivat/al-folio) to build my academic page. My system is Windows 10, and I spent some time to finally setup the page on local host and the Github Pages. 

The following steps are mainly based on [al-folio Getting Started](https://github.com/alshedivat/al-folio#getting-started). Please read through it first.

## Create a new repo
I use this template and create a new repository. Steps are [here](https://docs.github.com/en/repositories/creating-and-managing-repositories/creating-a-repository-from-a-template#creating-a-repository-from-a-template). In order to upload the page to my Github Pages later, I need to set the repository name exactly as the username `Perian-Yan.github.io`.
            
Once the new repo is created, I git clone it to my local directory: 

```sh
git clone git@github.com:Perian-Yan/Perian-Yan.github.io.git
cd Perian-Yan.github.io
```

## Setup webpage locally
First, check if Ruby, Bundler, and Jekyll have been installed on the device by checking the version:
``` sh
ruby -v
bundle -v
jekyll -v
```

{% include figure.html
path="assets/img/2023-09-28/image-2.png" 
class="img-fluid rounded z-depth-1 mx-auto d-block" 
zoomable=true 
width=500 %}


I haven't installed Ruby at the beginning, so I downloaded it from [RubyInstaller](https://rubyinstaller.org/). 

---
When I run `bundle install`, an error about mini_racer occurs:
{% include figure.html
path="assets/img/2023-09-28/image-3.png" 
class="img-fluid rounded mx-auto d-block"  
width=500 %}

This is because MiniRacer does not support Windows anymore (see [MiniRacer](https://github.com/rubyjs/mini_racer#supported-ruby-versions--troubleshooting)).

The problem, however, can be resolved by commenting out mini_racer and adding `gem 'wdm', '~> 0.1.0'` in the Gemfile, and installing [Node.js](https://nodejs.org/en/download), see this [thread](https://github.com/alshedivat/al-folio/issues/691#issuecomment-1309072582) for more details.

---
The next step is to install Jupyter, otherwise it will cause errors when you run `jekyll serve` later:

{% include figure.html
path="assets/img/2023-09-28/image-1.png" 
class="img-fluid mx-auto d-block" 
zoomable=true 
width=500 %}

I already have conda environment and Jupyter installed. Check the Jupyter's version in conda environment: 

```sh
jupyter --version
```

---
Finally, run `bundle exec jekyll serve` (add flag `--trace` or `--verbose` to show the detail if any errors happen). 

Before everything going well, I have to disable imagemagick in `_config.yml` file (set `enabled: false`). Otherwise, I get the error below:

{% include figure.html
path="assets/img/2023-09-28/image.png" 
class="img-fluid mx-auto d-block" 
zoomable=true 
width=500 %}

Now, I can run `bundle exec jekyll serve` smoothly and I can connect the local host `https://127.0.0.1:4000` to see my webpage.

{% include figure.html
path="assets/img/2023-09-28/image-5.png" 
class="img-fluid mx-auto d-block" 
zoomable=true 
width=500 %}
<div class="caption">
    Success!
</div>


## Deploy webpage to Github Pages
To deploy the website to Github Pages, I just followed the instructions [al-folio Deployment](https://github.com/alshedivat/al-folio#deployment). It will automatically deploy everything, which may take 1-2 minutes. If it fails to deploy, you can run workflow manually. 

## Enjoy!
Now everytime I change my page, I can view it locally. To view it in Github Pages, just `git push`!

### edit config file
Check the [tutorial video](https://www.youtube.com/watch?v=g6AJ9qPPoyc) to see how to edit the template. 

### insert images in posts
The side-effect of disabling imagemagick is that I can not use markdown version to insert images.

```md
![Some text](image.png)
```

The workaround is to follow the [template](https://alshedivat.github.io/al-folio/projects/1_project/), use html and always store images under `assets`. More details at [Bootstrap-Grid](https://getbootstrap.com/docs/5.3/layout/grid/) and [Bootstrap-Images](https://getbootstrap.com/docs/5.3/content/images/).


### edit cv
Changing `_data/cv.yml` has no effects on the webpage because `resume.json` will first be used if it's defined in `_config.yml`. To load the contents of `_data/cv.yml`, just commet out lines below "Get external JSON data" in `_config.yml`. See this [discussion](https://github.com/alshedivat/al-folio/discussions/1611).