---
layout: post
title:  "Paths in Python: Local"
date:   2020-09-06 13:54:00 +0100
category: words
---

This post will describe the benefits of `pathlib` over `os.Path`.

When using Python, we often need to interact with the filesystem, for instance to create, delete, read or write files, 
or to check if files exist. 
Since Python 3.4 the `pathlib` library introduced the `Path` class for this, which has some advantages over `os.Path`, 
which tends to return strings.

## Getting the cwd
Let's first familiarise ourselves with the syntax with a simple call to get the current working directory. 

With `os`:
``` python
In [1]: import os
In [2]: os.getcwd()
Out[2]: '/Users/Daniel/Desktop
```

Now, with `pathlib`: 
``` python
In [1]: from pathlib import Path
In [2]: Path.cwd()
Out[2]: PosixPath('/Users/Daniel/Desktop')
```

Whilst these two approaches are both simple, we are introduced to a simple difference, the `pathlib` approach returns a 
`PosixPath` class due this being run in a Unix-like environment. This is a subtle but important distinction, which the 
next example will bring out.


## Check if file exists
With `os`:
``` python
In [1]: import os.path
In [2]: os.path.exists(os.path.join(os.getcwd(), 'test.txt'))
Out[2]: True
```

Now, with `pathlib`: 
``` python
In [1]: from pathlib import Path
In [2]: Path.cwd().joinpath('test.txt').exists()
Out[2]: True
```

Using `pathlib` means we can method-chain, so the code is a lot more readable than the `os.path` equivalent which needs 
nested calls. Method-chaining also makes it easy to use your IDEs auto-complete to understand all the available 
functionality.

## Navigate the file tree

With `os`:
``` python
In [1]: import os.path
In [2]: os.path.dirname(os.path.dirname(os.getcwd()))
Out[2]: '/Users'
```

Now, with `pathlib`: 
``` python
In [1]: from pathlib import Path
In [2]: Path.cwd().parents[1]
Out[2]: PosixPath('/Users')
```

In this case `pathlib` avoids nested function calls and method-chaining entirely. Instead we access all the possible 
parents of the path with the `.parents` attribute and obtain the appropriate parent.

``` python
In [1]: list(Path.cwd().parents)
Out[1]: [PosixPath('/Users/Daniel'), PosixPath('/Users'), PosixPath('/')]
```

## Example Use
`pathlib` classes work with pandas for reading and writing:
``` python
In [1]: iris_df = pd.read_csv(Path.cwd().joinpath('iris.csv'))
In [2]: iris_df.to_csv(Path.cwd().joinpath('iris2.csv'))
```

We can also use them when reading and writing files with the standard library:
``` python
In [2]: with open(Path.cwd().joinpath('iris.csv')) as f:
    ...:     print(f.readline()) 
"sepal.length","sepal.width","petal.length","petal.width","variety"
```

Although for this simple case we can just use `.read_text()` method:
``` python
In [1]: Path.cwd().joinpath('iris.csv').read_text()[0:500]
Out[1]: '"sepal.length","sepal.width","petal.length","petal.width","variety"\n5.1,3.5,1.4,.2,"Setosa"\n4.9,3,1.4,.2,"Setosa"\n4.7,3.2,1.3,.2,"Setosa"\n4.6,3.1,1.5,.2,"Setosa"\n5,3.6,1.4,.2,"Setosa"\n5.4,3.9,1.7,.4,"Setosa"\n4.6,3.4,1.4,.3,"Setosa"\n5,3.4,1.5,.2,"Setosa"\n4.4,2.9,1.4,.2,"Setosa"\n4.9,3.1,1.5,.1,"Setosa"\n5.4,3.7,1.5,.2,"Setosa"\n4.8,3.4,1.6,.2,"Setosa"\n4.8,3,1.4,.1,"Setosa"\n4.3,3,1.1,.1,"Setosa"\n5.8,4,1.2,.2,"Setosa"\n5.7,4.4,1.5,.4,"Setosa"\n5.4,3.9,1.3,.4,"Setosa"\n5.1,3.5,1.4,.3,"Setosa"\n5.7,3.8,1.7,'

```

If we need to ever convert paths to strings we can use the `.as_posix()` attribute.
``` python
In [1]: Path.cwd().joinpath('iris.csv').as_posix()
Out[1]: '/Users/Daniel/Desktop/iris.csv'
```