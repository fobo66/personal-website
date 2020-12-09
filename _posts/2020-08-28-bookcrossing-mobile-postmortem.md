---
title: "Bookcrossing Mobile: post mortem"
published: true 
---

Hello! Recently I received copyright infringement notice about my favorite pet project, Bookcrossing Mobile app, and was asked to remove the app from Play Store. On this sad note, I decided to discontinue its development completely and keep its code open and untouched. In this article, I will describe my journey with development of this project, some cool tricks I've learned, as well as provide some description of the project for anyone who may be interested in it. Project will rot quickly and will become mostly unusable after a short period of time (but still may serve as a reference), but my experience working on it might be useful for those who are looking to start some mobile app project alone.

## Backstory

I've started this project to explore RxJava and Firebase, also to build up my portfolio and to learn. Idea came to my mind when I was browsing Play Store in search of the app to exchange books. I've been participating in bookcrossing movement since 2010, when the first shelf appeared in my hometown. It was not systemized, and a lot of books were not registered on the website, mostly because at that time site was quite inconvenient to use, and, if I'm remembering correctly, site was only available in English. So, after not finding the official Bookcrossing app on the Play Store, I decided to create my own app for bookcrossing that will make it easy to release new books and that is convenient to use.

I've created a list of desired features, but there was almost immediately appeared one more problem: main bookcrossing website didn't have any API available. So I had two options: HTML parsing on client to extract some data or go with my own backend.

I've tried to go with Google services, and started researching Google Cloud Platform stuff for backend. GCP is cool, and it offers a lot of really good services, but it all required a lot of effort to invest, and that wasn't what I wanted. Ideally, I would have preferred to spend as little time on backend development as possible, so I can focus mostly on app side.

Thus, I turned my look to Firebase. It was at that time recently acquired by Google, and there was not so much services they offered. But it was quite enough for my needs: Realtime Database was quite enough to store basic data that I had, Cloud Storage fitted perfectly for storing cover images, and Authentication worked just fine for handling users. Plus later I've added Ads and Analytics as a side effect. There was even convenient FirebaseUI wrapper that helped with binding Firebase services with UI.

I had an eye on RxJava for a long time, it seemed for me the good choice for the most Android apps due to its good threading abstractions, concise API and functional programming fleur. Reactive programming didn't seemed too complex for this project for me, and I wanted to improve my knowledge of Rx, so I decided to build my app in Rx-way.

These factors shaped the initial architecture of the app and helped decide how it will look like.

## Initial development

Once I figured out what to do, I chose then-popular MVP architectural pattern for my new app that was implemented with help of Moxy library. It was quite straightforward, given the amount of code on Stackoverflow ready for copying and pasting right into the project. For non-trivial issues I've picked some libraries from Github with decent amount of stars. I followed examples for Firebase setup and for Moxy, without quite thinking about fitting it into the MVP pattern, so I ended up with a lot of Firebase-related code in views and a lot of business logic in presenters, all mixed up. I even tried to write some tests, but it looked for me like there was not so much to test in terms of business logic, so I haven't added any tests. To be frank, it wasn't quite possible to add tests in that situation, because of a coupling business logic with UI, as well as lack of support for tests on Firebase side.

## Release

## Big refactoring

## State after refactoring

## Buried plans

## Conclusion
