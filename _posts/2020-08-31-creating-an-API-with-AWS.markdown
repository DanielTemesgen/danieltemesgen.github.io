---
layout: post
title:  "Creating an API with AWS"
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

### A discussion of terms
Before we go on, it may useful to briefly talk through some terms which we'll be using through this post.

### AWS
  Amazon Web Services provides on-demand cloud computing services, imagine using a huge amount of computation power for an ETL job but only paying for the amount you used, or building a database and being billed on each query. It's a suite of computing products that one can use to build an IT infrastructure.
  
### API
Application Programming Interfaces are standardised ways in which computational process can talk to each other. An API defines how this conversation occurs, how errors are defined, what information can be requested and responded to. For the purpose of this post think of it as a handshake between two computers!

### Serverless
  Right, now this may be somewhat counterintuitive, calling something serverless is a lot like saying ordering a takeaway is _kitchenless_, clearly a kitchen was used to cook the food, you just didn't have to to manage or maintain it, it's been abstracted away. _Serverless_ applications simply abstract the maintainence of servers away from a developer so you don't need to worry about provisioning your own servers for a project. It has a few advantages, for instance we only pay for what we use, the servers aren't assigned to our process unless they're being used, my API isn't going to be used 24/7 so why pay for a server to be hosted for that time?

### Frontend
A frontend is just a nice user interface, typically it has buttons and makes things look nicer for users who don't like their data served as an unformatted json.

## AWS


## Technical Solution


**link to tutorials**
