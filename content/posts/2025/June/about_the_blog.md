+++
date = '2025-06-16T11:36:17Z'
draft = false
title = 'Hugo vs Flask'
auther = 'Moriel Mauni'
tags = ["blog", "hugo", "Flask"]
categories = ["code"]
description = "To be or not to be, using a known framework as Flask or learning on go with Hugo?."
+++

## Choices
At first, I wanted to make this blog with Python Flask. That the framework I know how to work with, not flawless but I know my way in it.
I come from the world of System and DevOps and I don't know any "Web building frameworks".

### Flask

I met Flask the first time a year ago, I was learning Python and part of the course was about web apps and I mage a demo blog just to get the experience.
[Blog with Flask repository](https://github.com/MorielMauni/Blog_With_Flask)

I even started to blog, picked a Bootstrap theme and organizing the HTML files as Django so it will be easy to keep up with all the pages.
Let me show you:

This is the index.html
```
```
```HTML
{% extends "base.html" %}
{% block title %}Moriel's blog{% endblock %}

{% block content %}
<div class="row">
    <div class="col-lg-8">
        {% include '_post.html' %}
        {% include '_comments.html' %}
    </div>
    <div class="col-lg-4">
        {% include '_sidebar.html' %}
    </div>
</div>
{% endblock %}
```
```
```

All the base framework is done as you can see in the repository I already made all the base just to make everything easy.
[blog-2025: Templates](https://github.com/MorielMauni/blog-2025/tree/master/templates)

#### Why I didn't continue?

I got a 2 domains to date:
1. [morielmauni.com](https://morielmauni.com): I bought this domain from AWS Route 53 almost a year ago. I host there my protfolio. To be honest, the service is very expensive for static web.
2. [morielclouds.com](https://morielclouds.com) I bought it around 2-3 months ago to host my apps from my [HomeLab](https://github.com/MorielMauni/homelab-2025) from CloudFlare. 

The obvious thing is to host it on CloudFlare, it's just more easy. But when I looked on how to host a Python web app I found out that I need a service that can run the Python and then CName the service to CloudFlare. Well, this sucks. 

**I'm a DevOps** I want things to work flawlessly, I don't need to be depend on 2 services just to host 1 static web app. And even more, few days ago CloudFlare was down because Google Cloud was down [Cloudflare Blames Google Cloud For Mass Services Outages](https://www.crn.com/news/cloud/2025/multiple-cloud-services-down-as-google-cloudflare-resolve-issues). I need my apps to be app and running all the time, no down times.

### Hugo 

I was searching the web to see what other people do. Then a video of [NetworkChuck](https://www.youtube.com/@NetworkChuck) I saw he was hosting a blog from Obsidian. My head exploaded. Writing to a blog, without divs or this p thingy, just writing plain English, no AI use to write my website of choice, WOW!.
NetworkChuck guide was kind of chunky for what I want. The use of Obsidian is good, everyone use it this days, but I "Open Sourced" the vault (we can talk about it in another day).
So as every good developer, I started with the documentation [Hugo documentation](https://gohugo.io/getting-started/quick-start/), made a clean new DevPod for tests and started working.

The installation was easy, even the first website creation was easy as:
```bash 
hugo new site <name>
```

The step by step guide going to be on another time but it is that easy.
Even this blog, while being written in just English, with the "MarkDown" language is something that I'm used to thanks to Obsidian.


### Personal note 

The goal is to add a post each and every day. But life as life not always simple and easy, but I will do my best. 

