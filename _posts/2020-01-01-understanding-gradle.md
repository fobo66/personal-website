---
title: "Understanding Gradle for Android developers"
published: false
---

Hello! For a long time I wanted to migrate my [Bookcrossing Mobile](https://github.com/fobo66/BookcrossingMobile) app to Gradle Kotlin DSL.
But I've used remote Groovy function for loading API keys from `.properties` files. Here is a [gist](https://gist.github.com/fobo66/17d5116b5c7bccf5f28036f401f3c09d).
Despite it's a simple function, I didn't wanted to write this for each of my projects, thus this gist was created.

I wanted to share this code in more common way, via plugin, so I decided to create my own plugin. After I was done with it, I realized how little I know and understand about Gradle.
Official docs are not clear enough and spread across different pages, and articles across the internet are often outdated. Getting the knowledge about Gradle helped me
understand how Android build system works, how can we effectively configure our builds and how to effectively automate some tedious tasks. So, I decided to write this article.

First of all, I'll explain a little bit about what are build systems and what are they useful for, with small historical reference to GNU make as an example, then I walk you through
the components of Gradle, and in the end we will fixate our knowledge on the concrete example of the Android Gradle plugin.

To read this article, you need to know any programming language, but familiarity with Java and Android development will help, since I will use a lot of terms related to it.

## What is a build system

According to [wiki](https://en.wikipedia.org/wiki/Build_automation), build automation system (or build system, for short) is "is the process of automating the creation of a software
build and the associated processes including: compiling computer source code into binary code, packaging binary code, and running automated tests". This definition may not tell you
enough, so let's look into an example.

Imagine your first Hello World application, when you just started to learn your programming language. It probably was deadly simple, written in one file and run from the command line.
