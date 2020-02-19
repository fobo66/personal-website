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

Something like this:

``` java
package hello;

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
project on different machine.

This was more painful in the late 1970s, when C programming language was on the rise. Software engineers were managing complex projects with lots of different files, and that files may
require special treatment (e.g.not just compile source code, but fix indentation levels first) it was quite difficult to keep track of the files that need to be recompiled when changes
occur, and that inspired Stuart Feldman to create [`make`](https://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.39.7058), one of the first build automation systems.

General idea behind `make` was this: gather source code files, together with dependencies and resources, compile them and produce executable file. If some files were changed, `make` will
recompile only those files and produce new executable.

To tell `make` what to do, special text file is used. It's called `Makefile` and it contains a set of rules on which `make` will treat your software. Makefiles are quite complex, but the
main thing that you need to know is that you can configure all the build process however you want. Wanna add static analysis before compilation, or need to convert all the images to WebP
format before producing JAR? It's a bunch of lines of config.
