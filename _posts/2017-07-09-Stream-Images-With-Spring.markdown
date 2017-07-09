---
layout: post
title:  "Stream Images With Spring"
date:   2017-07-09 00:00:00
categories: blog java spring
tags: java spring springmvc streaming
comments: true
---

In lot of social and other sites we see images don't load at once, they are incrementally loaded and rendered.
This is done by streaming the image contents, in this post I will write a small api to demonstrate how that can be achived.

At the end of this post we will see image is loaded incrementally like this -

<img class="project-image" src="{{site.baseurl}}/images/java/spring/streaming/StreamImage.gif" alt="Image Stream">


I will use Spring boot for quick set up, we will read image as byte array then will write to output stream.


### 1. Read the image in byte array, using Files.readAllBytes from *java.nio.file.Files*
    File fi = new File("/Users/Pratik/Desktop/me.jpg");
    byte[] fileContent = Files.readAllBytes(fi.toPath());

### 2. Set the Content Type Header in Response

Setting this header is important as browser will understand the content type as image only from this.
Here I am setting it as *image/jpeg*, set this according to used image type.

    response.setContentType("image/jpeg");

### 3. We will Use StreamingResponseBody to stream the body content
Using StreamingResponseBody we have to override writeTo method which gets OutputStream, in which we can write
image byte data and stream.
This by default sets **Transfer-Encoding:chunked**, so we don't need to set this header.

    return new StreamingResponseBody() {
    	            @Override
    	            public void writeTo (OutputStream out) throws IOException {
    	            	File fi = new File("/Users/Pratik/Desktop/me.jpg");
    	            	try {
    	        			byte[] fileContent = Files.readAllBytes(fi.toPath());
    	        			int offset = 0,
    	        				chunkLength = 10000,
    	        				fileContentLength = fileContent.length;

    	        			while (offset+ chunkLength < fileContentLength) {
    		                    out.write(fileContent,offset, chunkLength );
    		                    out.flush();
    		                    offset = offset + chunkLength;

    		                    if(fileContentLength < offset + chunkLength){
    		                    		chunkLength = fileContentLength - offset;
    		                    }
    		                    //Deliberately adding sleep to emulate buffering image
    		                    try {
    		                        Thread.sleep(500);
    		                    } catch (InterruptedException e) {
    		                        e.printStackTrace();
    		                    }
    		                }
    	        			out.close();

    	        		} catch (IOException e) {
    	        			System.out.println(e);
    	        		}
    	            }
    	        };

Here we use OutputStream's <a target="_blank" href="https://docs.oracle.com/javase/7/docs/api/java/io/OutputStream.html#write(byte[],%20int,%20int)">
write(byte[] b, int off, int len)</a> method, to write chunk of data in each iteration.

Above I am setting chunk length as 10000, which can be set according to your need.
Also I have added a delay of 500 mili seconds to emulate delay in reading, which can be case where
you are large reading file from input stream.

Run the Spring app and hit the url *http://localhost:8080/streamImage* to see the result.

Complete code can be found <a target="_blank" href="https://github.com/pagoenka/SpringStream">here</a>