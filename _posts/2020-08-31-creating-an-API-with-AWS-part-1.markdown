---
layout: post
title:  "Creating an API with AWS (Part 1)"
date:   2020-08-31 11:37:00 +0100
category: words
---

[test-api-link]: https://7z8boxyi92.execute-api.us-east-1.amazonaws.com/prod/stats/?numbers=5&numbers=6
[repo-link]: https://github.com/DanielTemesgen/aws_statistical_calculator

This post will describe what an API is, how we can deploy it on AWS, what AWS is, as well as the code behind the infrastructure.


Already have experience the above?
Feel free to have a look at the [repo][repo-link].
Have a look at the sample URL below for a look at how it works.
`https://7z8boxyi92.execute-api.us-east-1.amazonaws.com/prod/stats/?numbers=5&numbers=6`

There's a lot to unpack in this post so let's first describe the problem, then the solution in simple terms.

## Problem Description
Let's say you work at Ofqual, every year you're sent an array of each cohort's exam results to conduct some basic descriptive statistics for a report for higher-level management. The current process involves loading it into a database and running a basic SQL script. It works, but could be better. 
You'd rather democratise this process so any one of your colleagues can run it without need access to SQL server or knowledge of SQL.

## Proposed Solution
You want to turn this process into an API, so anyone with internet access can pass an array of exam scores into a URL and be presented with a json with the statistical description. You've heard a bit about AWS and think it may be a good platform to develop your project.

## AWS

## Technical Solution
Rather than..

**link to tutorials**

