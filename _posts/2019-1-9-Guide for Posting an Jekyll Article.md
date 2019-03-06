---
layout: post
title: Guide for Posting an Jekyll Article
categories: [jekyll]
tags: [guide, jekyll]
---

* content
{:toc}


# Name of the file

YEAR-MONTH-DAY-title.MARKUP

where:

- YEAR is a four-digit number
- MONTH is a two-digit number
- DAY is a two-digit number
- MARKUP is the extension, eg. md

# Content

```md
---
layout: post
title: A Trip
categories: [blog, travel]
tags: [hot, summer]
---

* content
{:toc}


# Welcome

**Hello world**, this is my first Jekyll blog post.

I hope you like it!
```

where:

- layout refers to the files under `_layouts`. It can be `post` and `default` by convention.
- `content {:toc}` generates the catalog
- two blank lines needed between the above things and the markdown content

# Run on the server

## Before You Begin

- Install RubyInstaller from https://rubyinstaller.org/
- Install jekyll by `gem install jekyll bundler`

## Start the server

```bash
jekyll serve
```

or 

```bash
bundle exec jekyll serve
```