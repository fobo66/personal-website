---
title: "Bookcrossing Mobile: post mortem"
published: false 
---

Hello! Recently I received copyright infringement notice about my favorite pet project, Bookcrossing Mobile app, and was forced to remove the app from Play Store. On this note, I decided to discontinue its development completely and keep its code open and untouched. In this article, I will describe my journey with development of this project, some cool tricks I've learned, as well as provide some description of the project for anyone who may be interested in it.

## History

I've started this project to explore RxJava and Firebase, also to build up my portfolio and to learn. Idea came to my mind when I was browsing Play Store in search of the app to exchange books. I've been participating in bookcrossing movement since 2010, when the first shelf appeared in my hometown. It was not systemized, and a lot of books were not registered on the website, mostly because at that time site was quite inconvenient to use, and, if I'm remembering correctly, it was only in English. So, after not finding the official Bookcrossing app on the Play Store, I decided to create my own app for bookcrossing that will make it easy to release new books and that is convenient to use.

I've created a list of desired features, but there was almost immediately appeared one more problem: main bookcrossing website didn't have any API available. So I had two options: HTML parsing on client to extract some data or go with my own backend.
