---
title: "Understanding Gradle for Android developers"
published: false
---

Hello! For a long time I wanted to migrate my [Bookcrossing Mobile](https://github.com/fobo66/BookcrossingMobile) app to Gradle Kotlin DSL.
But I've used remote Groovy function for loading API keys from `.properties` files. Here is a [gist](https://gist.github.com/fobo66/17d5116b5c7bccf5f28036f401f3c09d).
Despite it's a simple function, I didn't wanted to write this for each of my projects, thus this gist was created.

I wanted to share this code in more common way, via plugin, so I decided to create my own plugin. After I was done with it, I realized that it never would work in the
way I wanted it to work because of fundamental Gradle limitations. This situation made me think about how little I know and understand about Gradle. I've asked
colleagues, and turned out that they prefer not to touch Gradle much and only vaguely understand how it works.

Official docs are not clear enough and spread across different pages, and articles across the internet are often outdated. Getting the knowledge about Gradle helped me
understand how Android build system works, how can we effectively configure our builds and how to effectively automate some tedious tasks, as well as debunk some myths about it. So, I decided to write this article.

For me to properly understand Gradle, it was helpful to really understand the concept of the build system. Historical references helped me much here, along with
studying GNU make and Ant. So I decided to use similar approach in this article.

First of all, I'll explain a little bit about what are build systems and what are they useful for, with small historical reference to GNU `make` as an example, then I walk you through the main features of Gradle, and in the end we will fixate our knowledge on the concrete examples of the Gradle plugins that you can use for Android
development.

This is not a promotional article, neither it's a comprehensive analysis of the Gradle features. I will point out to some things which I find important and underrated,
and describe them with some pros and contras that I see.

To read this article, you need to know any programming language, but familiarity with Java and Android development will help, since I will use a lot of terms related to it.

## What is a build system

According to [wiki](https://en.wikipedia.org/wiki/Build_automation), build automation system (or build system, for short) is "is the process of automating the creation of a software
build and the associated processes including: compiling computer source code into binary code, packaging binary code, and running automated tests". This definition may not tell you
enough, so let's look into an example.

Imagine your first Hello World application, when you just started to learn your programming language. It probably was deadly simple, written in one file and run from the command line.

Something like this:

``` java
public class HelloWorld {
    public static void main(String... args) {
        System.out.println("Hello World!");
    }
}
```

It can be easily compiled with one command:

``` bash
javac HelloWorld.java
```

However, when complexity of the problem you solve grows, one file is often not enough. You may end up using multiple files, stored chaotically across the folder with your project.
And when you need to use some third-party dependencies, command for compiling stuff will grow significantly, allowing pesky mistakes spill into it when someone will try to compile your
project on different machine. You may end up with some `.sh` or `.cmd` files that will contain command that is required to build your project, but these scripts are not guaranteed to
work on other machine.

This was even more painful in the late 1970s, when C programming language was on the rise. Software engineers were managing complex projects with lots of different files, and that files may
require special treatment, e.g.generating some code before compilation. It was quite difficult to keep track of the files that need to be recompiled when changes
occur, and  no one wanted to fully recompile whole project when only one file was changed, because it can take ages. That inspired Stuart Feldman to create
[`make`](https://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.39.7058), one of the first build automation systems.

General idea behind `make` was this: gather source code files, together with dependencies and resources, compile them and produce executable file. If some files were changed, `make` will
recompile only those files and produce new executable. These steps can be easily applied not only to the software projects, but for anything that involves files.

To tell `make` what to do, special text file is used. It's called `Makefile` and it contains a set of rules on which `make` will treat your software. With `Makefile` you can configure all the
build process however you want. Wanna add static analysis before compilation, or need to convert all the images to WebP format before producing JAR? It's a bunch of lines of config.

`make` helps to deal with a lot of source code files in a systemized way, and you can be sure that your software will be build on different machine exactly as it's built on yours.

So, the concept of the build automation system can be summarized like this: _perform commands defined in config file on the given set of files._

## What is special about Gradle

`make` may look like it's a perfect fit for the job as the general purpose build system, but why there are the hell lot of other build systems?

Well, the common complaint on `make` was that it has over-complicated `Makefile` format, so project configuration often takes some time, and if you make some mistake in it, it's quite difficult
to properly debug it and locate the problem. In addition, `make` doesn't offer any solution for managing dependencies: you need to manually maintain them in order somehow, and that often leads
to heavily outdated dependencies and inability to update them, unless you will do it constantly.

### Reproducible builds

Many other build systems were intended to simplify configuration files, make builds reproducible and improve performance of the builds. Also, different build systems tend to solve different problems faced by their developer.
Gradle was initially created to make highly portable build environment, so any developer can have the same build output on any hardware used. Plus, Gradle developers strived to make dynamic build scripts, so you can write build
logic right in your build config, and be sure that it will be executed in any environment.

Huge library of built-in plugins allows you to start building right away, without spending much time on configuring builds.

### Caching

Important Gradle feature for Android devs is build cache support. It basically means that task won't be executed if its input files were not changed.
Caching system is robust and you can be sure that it works just fine all the time. I've seen some developers don't trust Gradle caches and execute `clean` task on CI every build, increasing build times with no reason.
For example, if your CI machine performs builds on different branches of you projects that have different version of the same library, builds will be executed correctly. Same goes for different Gradle versions. However, cleaning of caches is necessary to perform once in a while to ensure health of the build system.

### Plugins

Gradle Plugin system is great, because it allows you to automate project setup of your app for production, e.g. signing, API keys, localisation and eveything specific to your business.
However, Gradle provides a lot of different ways to setup these things, thus devs often abuse these features
