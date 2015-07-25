---
layout: post
title:  "Reading file, line by line in Go lang"
date:   2015-07-18 11:57:49
categories: blog golang
tags: golang go
comments: true
---
Reading from file line by line is quite simple in golang, and can be done in few lines of code, let see how its done.

Here in this post, I will be using scanner from [bufio](https://golang.org/pkg/bufio/) package for it.
First we will need package [os](https://golang.org/pkg/os/) for opening file from its location.

{% highlight Go %}
	fileHandle, _ := os.Open("file_name.txt")
{% endhighlight %}

You can pass relative(../file_name) or abosulte path(C:/folder_name/file_name) of file to Open method.

We will add a defer statement to close file, so that once our work is done file handle is closed, <code>defer fileHandle.Close()</code>

Now we need Scanner for reading line, we can create it using [bufio](https://golang.org/pkg/bufio/) package as 

{% highlight Go %}
	fileScanner := bufio.NewScanner(fileHandle)
{% endhighlight %}

Now scan line by <code>fileScanner.Scan()</code> and get its content as string as 
<code> fileScanner.Text() </code>

We can use fileScanner.Scan() in for loop to determine end of file.

The complete code is as below, here I am reading line by line from file and printing it on console

{% gist 80fd2faa2ee2ac5b646d %}

<strong> Note:- </strong> I am using "_" for error, and not handling the error, but should be done in your production code, read about error handling [here](http://blog.golang.org/error-handling-and-go)