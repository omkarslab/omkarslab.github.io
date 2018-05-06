---
id: 594
title: Splitting long hangfire jobs into smaller ones
date: 2016-12-21T19:07:49+00:00
author: Omkar
layout: post
guid: http://omkarslab.com/?p=594
permalink: /2016/12/21/splitting-long-hangfire-jobs-into-smaller-ones/
sfw_pwd:
  - rDF0afYBqi3C
categories:
  - Developer
  - General
tags:
  - Background Job
  - Batch
  - Continuation
  - Hangfire
---
I recently came across a scenario where I needed to implement a long running job, that could probably take more than 8 hours to finish the desired task. After a ton of unpleasant experiences with Web Jobs, I made Hangfire as my go to library for long running jobs. But this isn&#8217;t about Hangfire, or any specific library. This approach would work as well for any alternative that does not readily provide batch continuation (like Hangfire Pro does, but it comes at a price $$$).

Here&#8217;s the scenario. I am consuming a web service that provides a list of notifications that my app has missed. Each notification object contains a URL of the content that needs to be retrieved and analyzed.

## Solution I: One job to rule them all

I think the diagram below is self explanatory.

<img class="aligncenter size-full wp-image-597" src="http://omkarslab.com/wp-content/uploads/2016/12/1.png" alt="" width="586" height="671" srcset="http://omkarslab.com/wp-content/uploads/2016/12/1.png 586w, http://omkarslab.com/wp-content/uploads/2016/12/1-262x300.png 262w" sizes="(max-width: 586px) 100vw, 586px" />

### Problem

Executing a long running job in a single thread like this can be very time consuming. While a long running job is bound to run into problems, even with re-entrant code this could waste hours of CPU time.

## Solution II: Divide and conquer

We can improve our original solution by passing each notification object to a new job (**Job B**) that will further fetch the content object and analyze it. Thus splitting **Job A** into multiple jobs. Here we have the benefit of utilizing multiple threads, speeding up the execution of **Job A**.

<img class="aligncenter size-medium_large wp-image-601" src="http://omkarslab.com/wp-content/uploads/2016/12/2-768x425.png" alt="" width="768" height="425" srcset="http://omkarslab.com/wp-content/uploads/2016/12/2-768x425.png 768w, http://omkarslab.com/wp-content/uploads/2016/12/2-300x166.png 300w, http://omkarslab.com/wp-content/uploads/2016/12/2.png 796w" sizes="(max-width: 768px) 100vw, 768px" />

### The Problem

Hangfire Jobs have to be <a href="http://docs.hangfire.io/en/latest/best-practices.html" target="_blank">re-entrant, or idempotent</a> based on your level of expertise in programming theory. If Job A fails in retrieving the notifications due to a network or service unavailable error, Hangfire&#8217;s transient fault tolerance mechanism will kick in, and trigger the job again after a pre-configured duration. This behavior puts us at risk of creating duplicate jobs. A simple solution would be to ensure Job B is re-entrant such that duplicates do not interfere with business logic, but this comes at the cost of CPU accompanied in most cases with Bandwidth and Storage. Imagine this happening for a million incoming notification objects. Back to the whiteboard.

## Solution III: New world order

We were pretty close to solving the problem in the previous solution. We will simply modify it a bit.

<img class="aligncenter size-medium_large wp-image-602" src="http://omkarslab.com/wp-content/uploads/2016/12/3-768x501.png" alt="" width="768" height="501" srcset="http://omkarslab.com/wp-content/uploads/2016/12/3-768x501.png 768w, http://omkarslab.com/wp-content/uploads/2016/12/3-300x196.png 300w, http://omkarslab.com/wp-content/uploads/2016/12/3.png 859w" sizes="(max-width: 768px) 100vw, 768px" />

  1. In **Job A**,  we will create a Unique ID (GUID) on every invoke, and use this as a Batch ID to refer to.
  2. Instead of triggering **Job B** directly; we store the Content URL (Job description) in a database with the Batch ID.
  3. We create a new Job definition (**Job X**) that accepts a Batch ID and will fetch all Job descriptions with the Batch ID.
  4. Each Job description is then iterated through and the Content URL is fetched to enqueue an instance of **Job B**.
  5. After each successful enqueue, we will mark the Job description as done.

If Job A fails while iterating over notifications, Job B duplicates will not be created as we have only stored their Job description. On the next successful respawn of Job A a new Batch ID will be created. If Job X fails, while iterating through Job descriptions, we have been marking Job descriptions as done. So on the next respawn only the Job descriptions that were previously not added to queue will be picked up. Order restored.

There is certainly room for improvement, by making **Job A** more efficient at handling its own state when dealing with a powerful API such as OData. This case is fruitful for all scenarios irrespective of the flexibility provided by the service **Job A** is dependent on. Got suggestions or improvement, feel free to buzz me or leave a comment below.