---
layout: post
title:  "Testing JSON APIs with Quizzical"
date:   2013-07-15 22:35:00
categories: testing node
excerpt: "Testing is a very important part of development that aims to verify your code. Beyond that, testing also takes away any fear of refactoring, assuring that you don't break anything. Despite these benefits, some developers find it too much an inconvenience to bother with, especially when dealing with complex problems like asynchronicity and concurrency. However, the tool I've been working on, Quizzical, is a suite of scripts that aims to make writing these tests as easy as possible."
---

Testing is a very important part of development that aims to verify your code. Beyond that, testing also takes away any fear of refactoring, assuring that you don't break anything. Despite these benefits, some developers find it too much an inconvenience to bother with, especially when dealing with complex problems like asynchronicity and concurrency. However, the tool I've been working on, Quizzical, is a suite of scripts that aims to make writing these tests as easy as possible.

On APIs
-------
APIs (Application Programming Interfaces) provide endpoints for use within programs allowing one application to work with another. We will be discussing web APIs, like Twitter's, Facebook's or one that you will be writing soon. There are a couple things all of these APIs have in common: (a) all will be web APIs, and (b) all will serve JSON responses. These criteria are the only ones required for Quizzical to work for you.

On CURL
-------
CURL is a Unix utility for the performance of HTTP requests of all kinds. CURL is super-easy, extremely versatile, and clear-and-concise. It's no surprise that it is the favorite testing tool of many web developers.

Quizzical doesn't aim to dethrone CURL, but rather, to introduce it to automated testing. Automated Tests take a list of assertions to check, such as, "That button should be blue", and determine that it has either passed or failed.

Quizzical
---------
###Getting Started
Quizzical solves a very specific problem - testing a JSON API with CURL. CURL has a very clear and concise notation for expressing the specifications of an API in docs, etc. Additionally, CURL is a cross-platform standard in HTTP requests. All tests are of the following form.

{% highlight bash %}
curl -s http://localhost:8080/api/boards/123 | $assert "board access" success=true
{% endhighlight %}

The `curl` progress dialogues are suppressed with `-s`, and then the response is piped to the assertion checker script. A name for the test and criteria are passed as arguments. These criteria are assessed on the JSON response, with `@` being a special symbol for equality with the entire response, e.g., `@={}`.

###Utility
What advantages does Quizzical introduce? Quizzical makes testing as easy as CURL, and as pretty as CURL. That may seem trivial, but it can really lower barrier to entry into testing, and make it as friendly as possible.

Beyond ease of use, Quizzical also makes tests look like documentation. Programmers of any language are familiar with CURL and so your tests will fully communicate the details of the web request at hand.

Test-Driven Development
-----------------------
There is a methodology in programming known as test-driven development. Test-driven development entails writing tests first, and passing them second. Hence the usual workflow would be along the lines of writing application specifications, converting those into verifiable tests, and then passing those tests one by one.

Quizzical is great for use in test-driven development, because Quizzical tests look just like documentation. This cuts out a step in the process! Let's say you want to make a web-service returning information about animals. Then your specifications may look something like the following.

{% highlight bash %}
curl -s http://app.com/api/animals | $assert "List animals" first=Aardvark

curl -s http://app.com/api/animals/ox | $assert "Access Animal" name=Ox

curl -s http://app.com/api/animals \
	-d "{
		\"name\": \"Polar Bear\", \
		\"type\": \"Bear\"
	}" | $assert "Create Animal" success=true
{% endhighlight %}

These are very legible specifications with clear requirements, e.g., `first=Aardvark`, and more importantly, valid Quizzical tests.

Roadmap
-------
Quizzical is in the early stage of development. It was built to solve a very specific problem of mine and met my needs at the time. However, it needs more complex JSON assessment, and maybe sub-routines to be full-featured. Find Quizzical [on github](https://github.com/mattneary/Quizzical).