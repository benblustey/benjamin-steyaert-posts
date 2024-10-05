# Basic Documentation Site
Using the Chirpy theme to generate a documentation/blog site based on the MD files from my Obsidian Documentation.

## Chirpy Starter

Please see the [theme's docs](https://github.com/cotes2020/jekyll-theme-chirpy#documentation).

To fully use all the features of **Chirpy**, you need to copy the other critical files from the theme's gem to your
Jekyll site. The following is a list of targets:

```shell
.
├── _config.yml
├── _plugins
├── _tabs
└── index.html
```

To save you time, and also in case you lose some files while copying, we extract those files/configurations of the
latest version of the **Chirpy** theme and the [CD][CD] workflow to here, so that you can start writing in minutes.

## Prerequisites

Follow the instructions in the [Jekyll Docs](https://jekyllrb.com/docs/installation/) to complete the installation of
the basic environment. [Git](https://git-scm.com/) also needs to be installed.

## Installation

```console
$ bundle
```

#### Build the site and make it available on a local server
```
bundle exec jekyll serve
```
Browse to http://localhost:4000

#### Build the site for production
```
JEKYLL_ENV=production bundle exec jekyll build
```
Check the output content via http_server
```
npx http-server ./_site
```
After confirming everything looks good, scp contents of `_site` to DigitalOcean
