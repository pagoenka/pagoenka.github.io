---
layout: post
title:  "Writing to file in Go lang"
date:   2015-07-25 11:57:49
categories: blog golang
tags: golang go
comments: true
---
We saw how easy it is to read from file in golang in my previous post [here]({% post_url 2015-07-18-Reading-File-Line-By-Line-in-Go-lang %})

In this post, we will see how to write to the file, we will again use [bufio](https://golang.org/pkg/bufio/) package.

First create file to we wish to write to (It points to file, if it exist already)

{% highlight Go %}
	fileHandle, _ := os.Create("file_name.txt")
{% endhighlight %}

You can pass relative(../file_name) or abosulte path(C:/folder_name/file_name) of file to Open method.

We will add a defer statement to close file, so that once our work is done file handle is closed, <code>defer fileHandle.Close()</code>

Now we need Writer for writing line, we can create it using [bufio](https://golang.org/pkg/bufio/) package as 

{% highlight Go %}
	writer := bufio.NewWriter(fo)
{% endhighlight %}

Now simply write following line to write line to file -

{% highlight Go %}
	fmt.Fprintln(writer, "String I want to write")
{% endhighlight %}

And finally do not forget to flush writer as <code> writer.Flush() </code>
The complete code is as below -

{% gist 6a13e022785b552f1caa %}

<strong> Note:- </strong> I am using "_" for error, and not handling the error, but should be done in your production code, read about error handling [here](http://blog.golang.org/error-handling-and-go)

But if you have observed here, in case where file already exists, the file content are overwritten, so how to keep the original content and <strong>append</strong> text to file ?

To do that we will <strong>open</strong> file in <strong>append mode</strong> as 

<code> fileHandle, _ := os.OpenFile("output.txt", os.O_APPEND, 0666) </code>

Read about [OpenFile](http://localhost:6060/pkg/os/#OpenFile)

And rest remains the same as earlier, complete code -

{% gist 695e8d850c26aa7bb763 %}

We can combine previous [post]({% post_url 2015-07-18-Reading-File-Line-By-Line-in-Go-lang %}) code and this post code to read from file, apply some transformation if needed and then write/append to file.