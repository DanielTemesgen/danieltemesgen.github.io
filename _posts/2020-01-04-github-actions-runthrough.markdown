---
layout: post
title:  "GitHub Actions runthrough"
date:   2020-01-04 14:20:00 +0100
category: words
---

Note: this post assume some prior basic knowledge of git, such as the definition of terms like `commit` and `push`. 

A few months ago, Github finally announced native CI/CD, a key feature that they were lacking compared with competitors such as GitLab. For the purposes of this post think of Continuous Delivery as a remote server that can execute commands to your git repository based upon arbitrary actions. 

Those familiar with [IFTT](https://ifttt.com/) can think of CI as a more sophisticated version, with the ability to run scripts rather than predefined actions alone. 

So what may we use this tool for? Commonly users may want to set up unit tests to run automatically on every push, and reject the push if any tests fail. You can even do things like have the server tweet out the commit description or send you an email on every merge. 

A rather simpler example we'll cover in this post involves the repository I use to post python workshops I give from time to time. 

All I'd like to do is, on every push, convert the .ipynb notebook file to a set of jupytext html slides. The advantage of these slides being in an HTML format is that I can also use GitHub pages to host these statically for free!

So before we get into the details of how we do this, let's quickly go over the format of this solution. Ultimately, a  .yml file will run which controls the commands which the CI server executes. 

The  .yml file will contain bash-like commands that will run sequentially. 

Finally, a user can also use actions that others have developed. For instance, later on we'll get into an action which sets up a conda environment from a specified environment.yml file. 

Now let's breakdown the action for the workshop repo I mentioned. 

The below code block is the github action which runs on the repo. It's saved as main.yml in the hidden .github directory which exists in every GitHub repository.

{% gist 679c13887fa3be696b1e197f8e9201cd %}

Let's go through this, line by line. 

Firstly, we'll give our GitHub Action a name: Build Slides.

```

name: Build Slides
```

Next, we specify when we want this action to run, in this instance I'd like it to run on every push to this repository. 

```
on: [push]
```

The following option tells GitHub what operating system our remote server should be running, this isn't too important in our case, but let's say you were using this Action to do test a piece of software to ensure it performs correctly. 
You may want to ensure it works on Linux, MacOS and Windows systems.

```
jobs:
  build:

    runs-on: ubuntu-latest
```

Now we've defined the basic step, the following steps will run on this server.
Every step starts with a `Name`, which is what's shown to us whilst this runs, so it helps to be a bit descriptive.
Next, there's the optional `uses` step.

If you're using an Action developed by someone else then this is where you'd state what it is.
There can also be an optional `with` steps where you'd state the arguments for this action. We'll get into an example later.

Alternatively, other steps may require the `run` step instead.
This is where you'd put the actual commands you want the server to run, think of this as the commands that will run through a remote terminal, in this case on a Linux machine.

The first step uses the `checkout` Action, which performs `git checkout` on the master branch.
Now this enables the remote server to 'see' the master branch and act on changing it.

```
    steps:
    - name: Checkout master branch
      uses: actions/checkout@master
```

Now we'll setup a Python environment using the `setup-python` Action.

```
    - name: Set up Python 3.7
      uses: actions/setup-python@v1
      with:
        python-version: 3.7
```

Now we have Python, I'll explain the lines which occur in the `run` step:
* Let's use pip to install a virtual environment using the requirements.txt file in the repo.
* Then we'll go into the Decorators_Dataclasses_IDEs folder, which is the place with the notebook file we wish to convert.
* Then we use the `jupyter nbconvert` commman to convert the notebook to .html slides.


```
    - name: Install dependencies
      run: |
        pip install -r requirements.txt
        cd Decorators_Dataclasses_IDEs/
        jupyter nbconvert Decorators_Dataclasses_IDEs.ipynb --to slides --SlidesExporter.reveal_scroll=True
        mv Decorators_Dataclasses_IDEs.slides.html index.html -f
```
