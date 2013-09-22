---
layout: post
title: "Rails Test Learning"
date: 2013-05-14 09:47
comments: true
categories: 
---
As old days I programmed with Ruby, I always test my app by running it, so some problems will not appear until something match. 
What I lack is UnitTest and more test technique. With reading the book *The rails Way* I record my way of learning Rails test technique in this post.
<!--more-->
The built-in test framework in Rails is Test::Unit family of all xUnit. A subclass of Test::Unit::TestCase is the main part of Rails test.
As with other test framework, Rails Test consists of 3 parts: setup, test\_body, teardown.

 * setup: *In setup we do something prepared for test body. such as initialize an Object etc.*
 * test\_body: *In such test bodys, we do our purpose test work.*
 * teardown: *In teardown we do something that when test body finished.*
