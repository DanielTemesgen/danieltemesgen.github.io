---
layout: post
title:  "GitHub Actions runthrough"
date:   2020-01-04 14:20:00 +0100
category: words
---

Note: this post assume some prior basic knowledge of git, such as the definition of terms like: commit and push. 

A few months ago, Github finally announced native CI/CD, a key feature that they were lacking compared with competitors such as GitLab. For the purposes of this post think of Continuous Delivery as a remote server that can execute commands to your git repository based upon arbitrary actions. 

Those familiar with IFTT can think of CI as a more sophisticated version, with the ability to run scripts rather than predefined actions alone. 

So what may we use this tool for? Commonly users may want to set up unit tests to run automatically on every push, and reject the push if any tests fail. You can even do things like have the server tweet out the commit description or send you an email on every merge. 

A rather simpler example we'll cover in this post involves the repository I use to post python workshops I give from time to time. 

All I'd like to do is, on every push, convert the .ipynb notebook file to a set of jupytext html slides. The advantage of these slides being in an HTML format is that I can also use GitHub pages to host these statically for free!

So before we get into the details of how we do this, let's quickly go over the format of this solution. Ultimately, a  .yml file will run which controls the commands which the CI server executes. 

The  .yml file will contain bash-like commands that will run sequentially. 

Finally, a user can also use actions that others have developed. For instance, later on we'll get into an action which sets up a conda environment from a specified environment.yml file. 

Now let's breakdown the action for the workshop repo I mentioned. 

The below code block is the github action which runs on the repo. It's saved as main.yml in the hidden .github directory which exists in every GitHub repository. 


```

name: Build Slides

on: [push]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - name: Checkout master branch
      uses: actions/checkout@master
    - name: Set up Python 3.7
      uses: actions/setup-python@v1
      with:
        python-version: 3.7
    - name: Install dependencies
      run: |
        pip install -r requirements.txt
        cd Decorators_Dataclasses_IDEs/
        jupyter nbconvert Decorators_Dataclasses_IDEs.ipynb --to slides --SlidesExporter.reveal_scroll=True
        mv Decorators_Dataclasses_IDEs.slides.html index.html -f
    - name: Commit files
      run: |
        git config --local user.email "action@github.com"
        git config --local user.name "GitHub Action"
        git status
        git commit -m "Add changes" -a || echo "No changes to commit"
    - name: Push changes
      uses: ad-m/github-push-action@master
      with:
        github_token: ${{ secrets.GITHUB_TOKEN }}

```

Let's go through this, line by line. 
