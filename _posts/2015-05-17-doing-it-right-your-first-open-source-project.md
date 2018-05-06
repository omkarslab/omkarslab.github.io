---
id: 542
title: 'Doing it right: Your first open source project'
date: 2015-05-17T22:27:18+00:00
author: Omkar
layout: post
guid: http://omkarslab.com/?p=542
permalink: /2015/05/17/doing-it-right-your-first-open-source-project/
sfw_pwd:
  - W82UTdBx7BCZ
categories:
  - General
tags:
  - github
  - opensource
---
I do frequent rounds to Github to contribute to small projects or libraries I find useful. Generally my contributions come from issues I have had with the project, and identified a fix. Last year I made my first attempt to write <a href="https://github.com/omkarkhair/jsonTable" target="_blank">my own jQuery library</a>. Not meaning to make anything big, this was just something I would like to use for myself and thought about hosting it on github to feed my self-worth. It played pretty decently, and I noticed a few people using it as well. But something didn&#8217;t feel quite right.

Recently, I made another attempt. This time I wanted to keep things organized. I had realized that there is a difference between an _Open Source project_, and a _well maintained Open Source project_. As a developer, I would hate to rely on a project that is being least cared about the original author; unless I&#8217;m using it for a garage project. So what is it that makes a project well maintained?

I started working on <a href="https://github.com/omkarkhair/sp-jello" target="_blank">Jello</a> a few weeks ago. And here are the few things I made sure I followed. I might be stating the obvious here, in which case you are free to fly over to some <a href="http://theoatmeal.com/" target="_blank">more interesting stuff on the internet</a>.

Here is a quick checklist:

  1. **Add every feature you wish to implement as an _enhancement_ in the issue list.
  
** This helps you and your audience keep track of your progress, and you don&#8217;t miss out on crucials. Remember, people looking at your half-baked project can easily take a look at reported enhancements to know where your project is heading.
  2. **Create project milestones, and add issues to milestones.**
  
    Milestones can be scheduled. This gives your audience a clear idea about what to expect, and when. Once milestones are closed, these can be easily referred to create change logs.
  3. **Close or Fix issues with commits.**
  
    This leaves less ambiguity in your commits. Your audience now knows what just got fixed and how.
  4. **Assign yourself on any open issues you are planning to address.**
  
    This develops a sense of responsibility among contributors. Unallocated issues are bound to remain on the backburner, esp. when there is a volume of issues coming up. This also prevents any parallel planning during development.
  5. **Isolate the master branch from your development branch.**
  
    Agree or not, there can be a lot of half baked commits, reverts that you would not want on your master. It would just confuse your audience. A dev branch will make it easy for contributors. They can remain care free with their commits.
  6. **Mark releases.**
  
    Your audience might prefer to have the luxury of choosing an older build, as opposed to the latest one. Marking releases makes your development more reliable.
  7. **Go through some pain to write a contributor guideline.
  
** A good project will demand more than one contributors, and to make the on-boarding easy make sure you put a readme for your prospective contributors. Contributors should be instructed to describe their commits in details for pull requests to be merged.

&nbsp;

I will be trying to update this checklist as I proceed. Feel free to get back to be on twitter <a href="https://twitter.com/omtalk" target="_blank">@omtalk</a>, with any feedback, suggestions or rants.

&nbsp;