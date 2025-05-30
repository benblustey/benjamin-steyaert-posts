---
title: Obsidian2Jekyll
date: 2024-06-12
categories: Documentation
tags:
  - python
  - obsidian
  - unraid
image: /assets/img/posts/obsidian-jekyll.jpg
---

**Update: after working with AstroJS, my documentation site will be migrating over to Astro.**

Creating and maintaining documentation has always been a challenge for myself. In the past, [BookStack](https://www.bookstackapp.com/) was my goto for keeping notes, documentation on projects, logs, or even just text from clipboards while working. BookStack is a great platform, but the editor web ui was not mobile friendly, database was required, complex process for backups, large amount of overhead resources, single point of content creation, and many other pain points. If the process to document is not an easy process, I found keeping my documentation up to date was nearly non existent.
Just recently I was introduced to [Obsidian](https://obsidian.md/), which is a fantastic note taking application that can be self hosted and highly configurable with a large amount of community based plugins. Although I don't play DnD, there is a [fantastic site](https://www.obsidianportal.com/) that helps game players organize their campaign which are usually stored in massive binders. 

## Why Jekyll
* Content as code
* Git as a backup
* Static site generated
* Easier setup, with less dependencies
* Large community of plugins and themes
* Frontmatter YAML

## The Writing Process
Obsidian files are written in Markdown which is a very clean, concise, no-frills way of writing documentation. As a developer and tinkerer, MD files have been apart of my life since first learning Git almost 13 years ago.
To render MD files from Obsidian to Jekyll, some additional attributes are needed for propper display. That is where the front matter comes in to provide publish date, title, tags, categories, post image.

* Sync Obsidian MD files using iCloud
* Sync iCloud with unRaid using [icloud-docker](https://github.com/mandarons/icloud-docker)
* cron job or manually execute conversion script

### Example Front Matter
```yaml
---
title: NodeJS MongoDB API
date: 
categories: webdev, database
tags:
  - docker
  - NodeJS
  - MongoDB
  - homelab
image:
---
```

## The Script
This script takes an input directory and output directory, iterating over the MD files from the input directory. For the image in the front matter, the relative path is added and for the content images, special wrapper is added for the images that allow Jekyll to render them properly.

## Whats Next
* CDI Pipeline integration with Gitea
* performant images for different screen sizes
