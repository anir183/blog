---
title: Here we are!
date: 2026-07-16
description: I decided it was a good idea to try write a blog site. Why?
draft: false
type: post
author: anir183
tags:
  - yap
  - tech-talk
categories:
  - yapping
stage: budding
cover: https://raw.githubusercontent.com/foxihd/hugo-et-hd/master/static/svg/flowlines/7.svg
image: https://raw.githubusercontent.com/foxihd/hugo-et-hd/master/static/svg/flowlines/7.svg
---

Making a blog website is one of the staple beginner projects for web-developers. I did miss out on that, and have now caught up!

The main reason I initially wanted to write a blog was a little something called MAR points. Mandatory Activity Requirement points are something I need 25 of, to be allowed to move on to the next year of my college course. And apparently a blog qualifies for 20, I think. So a pretty good deal!

### What?
So... what would I even do in a blog then? Truth be told, I don't really know. This might very well be the only post to ever see the light of the sun, just to those juicy points.

But I do have some ideas for stuff I'd like to blog (if I remember to). I watch a YouTuber called "[ThePrimeagen](https://www.youtube.com/@ThePrimeTimeagen)", who does article reads. Some of my favorite ones like "Inner JSON Effect", "Dieselgate, but for trains.", etc. were from his streams.

So I'd like to do something like that - document my achievements, projects I've made, experiences I've had, devlogs, etc

### How?
Now that I had an inkling of an idea of what I want to do, I need to create a place to do it. Dev, Medium, HackerNews, etc., there are tons of platforms to do a bit of dev-related bloggin. There are many more for general purpose blogging. But that's boring, and as I said before, I hadn't made the staple blog project before.

Hence, I tried my hands at this. The TL;DR is a static hugo site with the hugo-brewm theme, pagefinder indexing and giscus comments. But the details will come in a bit.

### Why?
So why these technologies? Honestly, no particular reason. I just had a set of requirements, and these worked well for it.

Firstly, I planned to using GitHub pages to host the site; so it had to be static or at least the ability to build down to a static site.

Then, I would like the build process to be light and fast. Mainly, because the site "should" in theory be frequently modified to create new posts, and GitHub actions hours can pile up pretty quickly.

Finally, markdown. Markdown. Markdown. Every developer knows markdown to some extent, and now its getting more widespread due to AI. But markdown is definitely the simplest and easiest way to write rich text on the internet.

### Let's Start Building
Now that I had my toolbox ready, it was time to make stuff. I had recently finished my portfolio site ([plug](https://anir183.is-a/dev)), so I already knew what kind of design language I wanted. And, thanks to GitHub pages routing, I could just create another repository called "blog" which would route to `\<main site url\>/blog`.

So I was pretty much set on that side of things. I've had some dealings with hugo before. I think I even tried to create a portfolio site using hugo a couple years back. So I wasn't completely out in the water. I created a project and started browsing through the available themes.

[Hugo-Brewm](https://themes.gohugo.io/themes/hugo-brewm) caught my eye. It looked sleek, stylish, modern and minimalist without trying too hard. So I decided to use that as a base. And as it turned out, this theme also had built-in support for Search Indexing via as [Pagefind](https://pagefind.app/) well as Comments via [Giscus](https://giscus.app), so I was pretty damn happy.

### The Workflow
I had a project, a theme and related technicalities served on a plate. I just needed to do some plumbing and finish the job. Buuuuttt, I did NOT want to spend my time editing CSS and having to look at hugo-brewm's codebase to make any substantial edits.

So I did what any reasonable person would do in the year of our lord 2026. Sold my soul to the AI overlords! The site was done in about 15 commits after bringing in the brewm theme. 7 of them were fixes, 2 minor tweaks and 1 for GitHub action setup. So it was more like 5 commits worth of changes to the theme and there was my glorious blazing fast blog.

MUCH MUCH MUCH simpler that the development of my main portfolio site.

### Here we are
So after all that, here we are. Site - ready, blog - posted and points - at the end of the academic year. This was fun, a small fast project which did not need much thinking on my part honestly. But I do have a space now, to share my thoughts, experiences, learning, etc.

Maybe I'll add a newsletter next? So that my staggering 0 readers can stay up-to-date with my latest posts. But we will see.

That's it!