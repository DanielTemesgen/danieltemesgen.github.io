---
layout: post
title:  "Remote Paths in Python"
date:   2020-09-19 13:54:00 +0100
category: words
---
[local]: (2020-09-06-local-paths-in-python.markdown)
[pathlib]: https://docs.python.org/3/library/pathlib.html

> This post is a follow-up to the [post](local) on working with local filesystems in Python.

This brief post will describe the benefits of [`cloudpathlib`](cloudpathlib) in working with remote filesystems.

## Creating a Remote Filesystem
We need a remote filesystem to work with, we can use [`cloudpathlib`](cloudpathlib) against Azure, Google Cloud Storage, 
or S3-like environments. I haven't got access to an AWS S3 bucket, and you might not have access either, so to make 
this post reproducible we'll spin up a simple Minio instance. Minio is a lot of things, but for the purposes of this 
post we can think of it as a local S3-like filesystem.

The following two commands:
* Pull the Minio docker image.
* Run the Minio server locally, exposing the necessary ports.
```
$ docker pull minio/minio
$ docker run -p 9000:9000 -p 9001:9001 minio/minio server data --console-address ":9001"  
```
The terminal will print out the default admin credentials, user: `minioadmin`, password: `minioadmin`.
Let's create a test bucket to work with. We'll do this by logging into the Minio Console.
 
 ![create-bucket](/../assets/images/minio-create-bucket.png)
 
## Getting Started w/ [`cloudpathlib`](cloudpathlib)
To install the package use the following command, the `[s3]` means we'll also install the dependencies required to works
 with s3-like systems. 

```bash
pip install cloudpathlib[s3]
```

[`cloudpathlib`](cloudpathlib) has a fairly intuitive interface. We define an `S3Client` with the `endpoint_url` and 
credentials needed to access our Minio instance. The next line sets this client to be the default global client for all 
subsequent filesystem operations, we can of course have a more fine-grained approach and have different clients for 
different operations. The final line defines a `CloudPath` linked to the previously created `test` bucket.
 ```python
from cloudpathlib import S3Client, CloudPath
client = S3Client(endpoint_url="http://127.0.0.1:9000",
                  aws_access_key_id='minioadmin',
                  aws_secret_access_key='minioadmin',
                  aws_session_token=None)
client.set_as_default_client()
test_bucket = CloudPath('s3://test')
```

## Simple Operations w/ [`cloudpathlib`](cloudpathlib)
Now we'll join this bucket to `test.txt`, this path doesn't yet exist, we can check this with the `.exists()` method.
```python
test_path = test_bucket.joinpath('test.txt')
test_path.exists()
>>> False
```

From the code snippet above, we can see that this is very similar to the `pathlib` interface, the `joinpath()` method is 
the same. The line below writes the string 'This is a test.' out to the path. 
```python
test_path.write_text('This is a test.')
```

The code snippet below then reads the text from that file, we also check that it exists.
```python
test_path.read_test()
>>> 'This is a test.'
test_path.exists()
>>> True
```

## Example Use
[`cloudpathlib`](cloudpathlib) intuitively works with pandas for reading and writing:

```python
iris_path = bucket.joinpath('iris.csv')
iris_df = pd.read_csv(iris_path)
```

Writing is done via a simple context manager.
```python
iris_out_path = bucket.joinpath('iris_out.csv')
with iris_out_path.open('w+') as f:
    iris_df.to_csv(f)
```

## Caching
[`cloudpathlib`](cloudpathlib) comes with caching as standard.
For instance, when getting a file, `cloudpathlib` will check that the newest version of it exists in the cache.
If it does, the file will be taken from the local cache, saving time. If not, the file will be taken from the remote 
filesystem.

Let's use the small `test.txt` file as an example.
```python
%%time
with test_path.open("rb") as f:
    f
```
The first time we get it from Minio it takes ~60ms.
```python    
CPU times: user 32.1 ms, sys: 4.51 ms, total: 36.6 ms
Wall time: 61.7 ms    
```

This halves to ~30ms on the second retrieval, because the local cache is used.
```python    
CPU times: user 17.1 ms, sys: 2.83 ms, total: 19.9 ms
Wall time: 29.8 ms    
```

## AnyPath
AnyPath is a superclass of the [`pathlib`](pathlib) `Path` and the [`cloudpathlib`](cloudpathlib) `CloudPath` classes.
It's great when the input path could be local or remote.

For instance, when it's presented with an S3 path it returns an `S3Path`.
```python
In[1]: AnyPath('s3://test/test.txt')
Out[1]: S3Path('s3://test/test.txt')
```

When presented with a local path, then a local [`pathlib`](pathlib) `PosixPath` is returned.
```python
In[1]: AnyPath('words.md')
Out[1]: PosixPath('words.md')
```

Both `S3Path` and `PosixPath` support many of the same operations e.g. `.exists()`.

## Summary
Overall [`cloudpathlib`](cloudpathlib) is an intuitive way of working with remote filesystems. It's main strength 
is its compatibility with [`pathlib`](pathlib). Checkout [`the cloudpathlib docs`](cloudpathlib) for extra features that 
weren't explored here, like the built-in mocking functions, which make it easier to test code which work with remote 
filesystems.