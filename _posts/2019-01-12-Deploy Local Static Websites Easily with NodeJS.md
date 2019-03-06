---
title: Deploy Local Static Websites Easily with NodeJS
categories: ["javascript"]
tags: ["node","server"]
---


# Goal

I have some local web pages, js files and css files, and I want to deploy them on a local server to debug the website (for I cannot merely run the index.html on the browser because of some CORS problems).

# Steps

- Install NodeJS from [nodejs.org](https://nodejs.org)
- Install http-server from npm: `npm install -g http-server`
- Change directory (in the cmd prompt) to the root of you website folder where there must be an index.html, and run `http-server`
- Open a browser and enter the website according to the information given above.











