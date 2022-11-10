---
title: "Regex the way I like it"
date: 2020-10-22T12:19:51-04:00
draft: true
---

Regular expressions are two things:
* A tool for describing patterns in strings
* Frequently maligned and misunderstood

Hopefully, if you learn more about how they're helpful for the first point, the second point will become less true.

## (?:Opinion)+

I'm not going to try to explain regex comprehensively, or all the different ways that people like to do them.
The point of this article is to tell you how I go about producing them, and what I would expect out of a regex that I was looking at in a code review.

### The beginning and end of all things

Every regex should be as anchored as possible.
