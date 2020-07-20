---
title: "Musings about quality: what I've learned after migrating my projects to AndroidX"
published: true
---

Hello! Recently I decided to get back to my old project, [Bookcrossing Mobile](https://github.com/fobo66/BookcrossingMobile) app, to update it to the recent SDK versions and try out new things and approaches in Android development. Luckily, it was fine now, migration went smoothly, much better than at first attempt year ago.

However, there were some important points that I've discovered during migration, and I believe these point can simplify developer's life in the future. Hope that they will be useful for you.

## Background

Bookcrossing Mobile app was started as a pet project of mine. Due to limited resources development went slowly and often I didn't wanted to bother with some boring stuff and used a lot of third-party dependencies. Also, I thought back then that it's completely OK to mix up Rx and non-Rx ways. _Spoiler: it's absolutely not._

All of a sudden, after a few months of Bookcrossing Mobile being in Beta release, Google introduced replacement of Support libraries with AndroidX. I've immediately downloaded new Android Studio and launched migration. And... everything was completely broken. I'm sure some of you have seen this in your projects. For me it was frustrating experience. The only option was to wait until developers of the libraries I've used will upgrade to AndroidX, because I had been heavily dependant on these libraries. Even Jetifier was unable to help with the mess in third-party libraries, project didn't even compile because of them.

I was busy with other stuff, so I was unable to invest much resources into refactoring. Only after a year and a half I've finally got some time to work on this project again.

(Un)surprisingly, little has changed in terms of AndroidX support after a year. Some libraries
have migrated, but the majority of them stayed on support libraries, because they were no longer maintained.

Biting the tongue, I've migrated app to AndroidX and replaced every library that hasn't migrated. It took less time that I've expected, mostly because it was easy enough to replace outdated libraries with my own code.

This made me thinking about reasons why this happened and what are the ways to prepare

## Check code quality of the third-party dependencies if possible

Most of the pain was caused by the need of replacing abandoned third-party dependencies. My initial laziness in implementing some simple things turned against me. Some libs were easy to replace, and some require a lot of work.

Despite the amount of stars on Github, developers can lose interest or free time to work on their projects. Or these projects were not intended to be maintainable at all: developer just wanted to fix his own problem and then decided to extract his solution to the library. This library can solve one problem, but create another problem, and developer may not be interested in solving this another problem because of various reasons. In addition, library can contain some tricky workarounds that will make it unmaintainable if something inside Android SDK will change in the future. It is especially the case e.g. for some RxJava wrappers around permissions or `onActivityResult` handlers. In short, any third-party dependency is a risk, and amount of stars on Github doesn't matter here, but throwing money and resources into writing your own solution is even more expensive, so healthy compromise is needed.

To detect this kind of problems and reduce risks, consider checking the source code of the dependency. If it's written in a clean way, well-understandable and doesn't contain any tricky workarounds (like extensive usage of reflection, EventBus, etc.), then there is a high probability that it won't be abandoned or can be maintained by the community, or that you can fork it, figure out how it works and adapt for your needs.

Of course, there is no 100% insurance any library won't be abandoned. But when code is good, you can maintain it by yourself (because it's open-source) or implement similar code in your app using newer tools and in the way you need it in your app.

## Understand your architecture pattern and follow it everywhere

Architecture is your friend, but when you violate its rules, no matter intentionally or not,
it creates a tech debt that may not be easy to address in the future. And when the pile of tech debt grows, it will bury the whole project beneath its weight.

Aside pathos, architecture pattern (no matter which one in particular) gives you structure and decoupling of dependencies, so you can easily make changes in the future. But if its rules are not followed, e.g. you perform network request in presenter (or even in Activity) instead of data source or use data sources directly in the interactor instead of putting them in repository, you'll have problems in the future, because you may not be able to easily substitute your components or fix their implementation to comply with updated requirements.

Also, architecture is the fundamental thing that is too costly to change, and if you don't follow your architectural principles, it tends to be even more expensive.

## Abstract dependencies through the interface

I've noticed that developers (including myself) often tend to use third-party libraries directly, without abstracting them through an interface specific to the given circumstances. Most of the times it's fine, but in case of huge refactoring or breaking changes in newer version of the library, you may have to change the code in a lot of places, increasing chances to break it.

Of course, there are cases when it's impossible or unnecessary to abstract dependency, like in case of third-party UI components or RxJava chains. And that's OK. But in other cases it will be more future-proof to wrap third-party components into your custom interfaces, because then you will be able to replace one dependency with another without much pain.

There are more benefits in this, and [Uncle Bob will explain them better than me](https://drive.google.com/file/d/0BwhCYaYDn8EgN2M5MTkwM2EtNWFkZC00ZTI3LWFjZTUtNTFhZGZiYmUzODc1/view). I just wanted to highlight the most valuable point in terms of replacing broken dependencies with new stuff.

## Write truly testable code

Testability can mean a lot, but I wanted to pay attention to the one particular aspect here. Tests increase maintainability of your main code base, but they often are written in not so maintainable way. And from what I've seen, tests were the reason why projects have not been able to migrate to AndroidX. Which means that code under tests was not actually testable, so authors used some workarounds. And if even tests are built with crutches and duct tape, it can tell a lot about the quality of the main project.

Signals of such faux testable code can be PowerMock or Robolectric listed in dependencies.

Well, Robolectric can be really useful in some cases, but as any other tool, it can be abused heavily, making your tests fragile, so you'll end up maintaining your tests more than the main codebase.

PowerMock itself is a code smell, but if you forced to use some legacy code and you forced to test it somehow and you have no permission or no resources for refactoring, you have no other option. However, in this case it's reasonable to explain to your management that next step in Android SDK evolution may (and probably will) break your app completely, so you'll need to rewrite anything from scratch. Which means a lot of time and money thrown away. I was in the similar situation at my job once, so it's unfortunately a real case.

Try not to use Robolectric or mocking for things that are better tested as instrumented tests or Espresso UI tests, like things involving SDK classes, `XmlPullParser`, SQLite, etc. Nowadays it's not a big deal to run these kind of tests on CI, and they are not so slow as before (except you've got a really slow CI machine), and with services like [Bitrise](https://bitrise.io), it can be extremely easy to setup. Mocking Android dependencies may not be good solution here, unless you abstract them properly so they wouldn't affect your tests.

So, if you think that your code is good but you cannot easily cover it with tests, it may not be as good as you think. And this code will turn into nasty legacy code quicker than you can imagine.

## Conclusion

Follow SOLID principles from the beginning and your project will survive any Android SDK changes and you will save your mental health adapting to any changes in SDK or business needs. And if there is no architecture and tests in your project, prepare yourself for long and painful refactoring and your client for spending some time and money on this.

Hope that these quite random musings will be helpful for you. Reach me out directly in Twitter if you have any questions, happy to discuss. Cheers!
