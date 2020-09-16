---
layout: post
title:  "Creating a Serverless API with AWS and FastAPI"
date:   2020-08-31 11:37:00 +0100
category: words
---

[test-api-link]: https://7z8boxyi92.execute-api.us-east-1.amazonaws.com/prod/stats/?numbers=5&numbers=6
[repo-link]: https://github.com/DanielTemesgen/aws_statistical_calculator

This post will describe what an API is, how we can deploy it on AWS, what AWS is, as well as the code behind the infrastructure.

Already have experience on the above?
Feel free to have a look at the [repo][repo-link].
Have a look at the sample URL below for a look at how it works.

[![GitHub Repo](https://img.shields.io/badge/GitHub-Repo-blue)][repo-link]

`https://7z8boxyi92.execute-api.us-east-1.amazonaws.com/prod/stats/?numbers=5&numbers=6`

There's a lot to unpack in this post so let's first describe the problem, then the solution in simple terms.

## Problem Description
Let's say you work at Ofqual, every year you're sent an array of each cohort's exam results to conduct some basic descriptive statistics for a report for higher-level management. The current process involves loading it into a database and running a basic SQL script. It works, but could be better. 
You'd rather democratise this process so any one of your colleagues can run it without need access to SQL server or knowledge of SQL.

## Proposed Solution
You want to turn this process into an API, so anyone with internet access can pass an array of exam scores into a URL and be presented with a json with the statistical description. You've heard a bit about AWS and think it may be a good platform to develop your project.

## A discussion of terms
Before we go on, it may useful to briefly talk through some terms which we'll be using through this post.

### AWS
Amazon Web Services provides on-demand cloud computing services, imagine using a huge amount of computation power for an ETL job but only paying for the amount you used, or building a database and being billed on each query. It's a suite of computing products that one can use to build an IT infrastructure.
  
### API
Application Programming Interfaces are standardised ways in which computational process can talk to each other. An API defines how this conversation occurs, how errors are defined, what information can be requested and responded to. For the purpose of this post think of it as a handshake between two computers!

### Serverless
Right, now this may be somewhat counterintuitive, calling something serverless is a lot like saying ordering a takeaway is _kitchenless_, clearly a kitchen was used to cook the food, you just didn't have to to manage or maintain it, it's been abstracted away. _Serverless_ applications simply abstract the maintainence of servers away so you don't need to provision your own servers for a project. It has a few advantages, we only pay for what we use, I can safely assume my API isn't going to be used 24/7, so why pay for a server to be hosted for that time?

### Frontend
A frontend is just a nice user interface, typically it has buttons and makes things look nicer for users who don't like their data served as an unformatted json.

### Infrastructure-as-code
As mentioned above, AWS gives us a myriad lot of tools to work with. But how can we possibly configure all of that, in a way which is repeatable and clearly defined? Infrastructure-as-code allows a developer to state the configuration and management of AWS tools as code. Much in the same way as you may store settings in a config file.

## AWS
First of all, let's have a basic look at the code we're trying to turn into an API.
```
import scipy.stats as stats

description = stats.describe(numbers)
result = description._asdict()
```
It's quite simple, we take a list of numbers (called `numbers`) and use the `scipy.stats.describe` method to conduct some basic summary statistics on the list.
We then turn this into a dictionary called `result`.

## Technical Solution

### Why an API?
Before we dive into implementation it's worth taking a moment to ponder the benefits of this approach. 
For starters, APIs don't care who you are, by that I mean, they are accessible from any system that is capable of making an HTTP request. Python? ✅ Terminal? ✅ Chrome? ✅  Almost anything!

It's also a quite controlled solution, an API allows for authentication, endpoints mean only functionality you want to surface to your users is shared.
If we think of your backend as a mansion, an API allows you to only open certain doors, and keep others closed.

### Getting an AWS Account
Our first step is to get an AWS account to deploy our solution to.
If you're also a student, I'd recommend the [AWS Educate](https://aws.amazon.com/education/awseducate/) account, it's free, you get AWS credits and (importantly!) you don't need to provide any credit card information.

### Setting up an Example project
The first thing we want to do is set up an example project up, a basic API which returns "Hello World". This will provide us with a good basis to iterate on. This [tutorial series](https://www.youtube.com/playlist?list=PLyb_C2HpOQSDlnrNJ_ERqTAkIe21xyRhi) (up to Part 5) explains the basics of AWS and goes through an example deployment. The series will get you up until the point where you set the API up. 

The tutorials goes through the following:
*

Don't forget to:
* 

### FastAPI
It's at this point we diverge from the series and forge our own path! Rather than use Flask to set up our API we'll use a newer framework called [FastAPI](https://fastapi.tiangolo.com/). Unlike flask, this framework was designed explicitly to create APIs, and has a number of interesting features such automatically generated documentation which make it a great choice.

Let's jump right into the main code.

```
from typing import List
from fastapi import FastAPI, Query
from mangum import Mangum
import scipy.stats as stats

app = FastAPI(root_path="/prod")

@app.get("/simple")
def read_root():
    return {"Hello": "World"}

handler = Mangum(app, enable_lifespan=False)

if __name__ == '__main__':
    import uvicorn

    uvicorn.run(app)
```

The first four lines are simply importing packages we'll need to form the api. <br>
First we assign a FastAPI instance to a variable called `app`, we've also set the `root_path="/prod"` to ensure all defined endpoints are within this URL.

The next step is key, we declare a function called `read_root` (although the name isn't important). The function simply returns "Hello World" as a dicionary. Importantly we decorate that function with `@app.get("/simple")`, all that does is ensure that users must add `/simple` to their URL to return the output of the `read_root` function.

Now we can click through to [`https://7z8boxyi92.execute-api.us-east-1.amazonaws.com/prod/simple`](https://7z8boxyi92.execute-api.us-east-1.amazonaws.com/prod/simple), we should see the following:

```
{
"Hello": "World"
}
```
Just as we defined!

The following line is used to make FastAPI frameworks compatible with AWS Lambda environments.
And the final part beginning with `if __name__ == '__main__':` ensures we can run our API locally as well as on AWS.

### AWS Lambda Layers
