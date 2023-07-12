---
title: Will Android kill single activities in case it runs out of memory?
slug: will-android-kill-single-activities-in-case-it-runs-out-of-memory
date: 2017-11-06T19:35:49.000Z
template: "post"
draft: false
description: "I answered this question on Reddit, and I thought the answer might be interesting to document here as well, is there good documentation of Android lifetimes"
---

I answered this question on Reddit, and I thought the answer might be interesting to document here as well ([is there good documentation of android lifetimes](https://www.reddit.com/r/androiddev/comments/6zdi3p/is_there_good_documentation_of_android_lifetimes/))

### is there good documentation of android lifetimes

\*Meaning, let's say there's an OOM event (or Android needs to clean up RAM for another app). What will Android clean up?

For example, Activities and Services are independent (at least as far as I know), but each Activity is "atomic" (either it's running fine or it's 100% dead, you won't find that a in String a = "abc"; Log.d(TAG,a) will become null all of a sudden because of OOM).

But what about the Application class, custom singletons or static objects? Will Android keep them around until all services/Activities are dead (or, if it can't, will kill the whole process altogether)?\*

## No, Android doesn't kill single activities

Android kills processes.

Processes are roughly an app and everything in that app- all the activities, services, application class related to that particular app, in most cases one app equals one process.

That means, that if Android trimmed a process, all the activities (and everything else related to that app) inside it will also die as a result. Android (and in particular, the Android Kernel which is responsible for killing processes when reaching certain memory thresholds) doesn't trim single components within that process/ within that app, it doesn't even know or care that they exist.

It's worth noting that Android will trim processes based on a score that is based on their priority to the user, and not based on their memory usage. An app/ process can use a lot of memory but because it's significant to the user at a given moment (for example, it's the foreground app), it won't get trimmed unless under very extreme circumstances, and instead, apps that use less memory, but aren't that significant (for example apps that have been backgrounded), will get trimmed first to make room. Android scores apps based on the user experience, not based on how memory "hungry" they are.

In my talk "Android Internals for Developers" (can be watched here [https://skillsmatter.com/skillscasts/10526-android-internals-for-developers?tc=dde50b](https://skillsmatter.com/skillscasts/10526-android-internals-for-developers?tc=dde50b), requires sign in but it's free) I talk about these concepts and in particular:

- How processes correspond to apps/ app components (activities, services etc)
- How the Out of Memory Killer (or low memory killer) works
- How processes get their OOM score by the Activity Manager
