---
layout: post
title:  "SpiderMonkey Newsletter (Firefox 132-134)"
date:   2024-11-27 18:00:00 +0100
author: Matthew Gaudet
---

Hello! Welcome to another episode of the SpiderMonkey Newsletter. I’m your host,
Matthew Gaudet.

In the spirit of the [upcoming
season](https://en.wikipedia.org/wiki/Thanksgiving_\(United_States\)), let’s [talk
turkey](https://en.wiktionary.org/wiki/talk_turkey). I mean, monkeys. I mean
SpiderMonkey.

Today we’ll cover a little more ground than the normal newsletter.

If you haven’t already read Jan’s wonderful blog about how he managed to improve Wasm
compilation speed by 75x on large modules, [please take a
peek](https://spidermonkey.dev/blog/2024/10/16/75x-faster-optimizing-the-ion-compiler-backend.html).
It’s a great story of how O(n^2) is the worst complexity – fast enough to seem OK in
small cases, and slow enough to blow up horrendously when things get big.

### 🚀 Performance

- Awesome contributor Debadree [has added IC support for addition and subtraction on
  Date Objects](https://bugzilla.mozilla.org/show_bug.cgi?id=1490441)
  
- Jan [worked on saving fewer registers when calling out to C++
  code](https://bugzilla.mozilla.org/show_bug.cgi?id=1919217), providing a 5%
  improvement on some speedometer subtests.

- Jon [improved the performance of store buffer
  iteration](https://bugzilla.mozilla.org/show_bug.cgi?id=1920761) by avoiding
  linked-list traversal where possible.

- Jon [improved the performance of
  sweeping](https://bugzilla.mozilla.org/show_bug.cgi?id=1925581) by doing more work
  without holding a lock

- Jan [continued improving register allocation by choosing a better representation
  for sparse bitsets](http://bugzilla.mozilla.org/show_bug.cgi?id=1920430), improving
  compilation time on a wasm module by 40%, and improving some PDF test cases by 5%.

- Jan worked on [Map and Set
  Optimizations,](https://bugzilla.mozilla.org/show_bug.cgi?id=1851662) with
  improvements across a number of benchmarks.

### 👷🏽‍♀️ New features & In Progress Standards Work

- The WebAssembly Memory64 proposal has reached stage 4 and is enabled by default in
  Firefox 134. This proposal finally adds 64-bit pointers to WebAssembly—although
  this comes with some downsides, so stay tuned for a blog post exploring this
  subject further.

- Prolific contributor André has worked on over 30 (‼) bugs in this newsletter time
  frame. Feature wise he’s continued his incredible stewardship of the Temporal
  proposal, as well as [implementing Int.Duration
  format](https://bugzilla.mozilla.org/show_bug.cgi?id=1648139), providing [initial
  implementation of
  Atomics.pause](https://bugzilla.mozilla.org/show_bug.cgi?id=1930952).

- Speaking of great contributors: Debadree has continued work on the [Explicit
  Resource Management
  proposal](https://github.com/tc39/proposal-explicit-resource-management), with [the
  implementation looking like it’s in great
  shape](https://bugzilla.mozilla.org/show_bug.cgi?id=1569081).

- Dan has been on a roll shipping proposals:
  [Promise.try](https://bugzilla.mozilla.org/show_bug.cgi?id=1918235),
  [Regexp.escape](https://bugzilla.mozilla.org/show_bug.cgi?id=1918235), and [Regular
  Expression Pattern
  Modifiers](https://bugzilla.mozilla.org/show_bug.cgi?id=1913752).

### **🚉** SpiderMonkey Platform Improvements

- Jan has killed a whole class of errors that have [bedevilled SpiderMonkey
  developers for at least 15
  years.](https://bugzilla.mozilla.org/show_bug.cgi?id=552007) The root cause of the
  issue is the overloading of error checking inside of SpiderMonkey. The general case
  is “Report an Exception” and return false.

  The problem is that, returning false without setting an exception \-also- has a
  meaning: It’s throwing an uncatchable exception, which is used for things like
  unwinding when you stop a script due to the slow script dialog.

  So what happens if you were to accidentally return false without setting an
  exception: Did you mean to do that? Or did you forget? In [Bug
  1921215](https://bugzilla.mozilla.org/show_bug.cgi?id=1921215), [bug
  1921780](https://bugzilla.mozilla.org/show_bug.cgi?id=1921780), and [bug
  1921963](https://bugzilla.mozilla.org/show_bug.cgi?id=1921963) Jan has tidied this
  whole story up with a new helper `JS::ReportUncatchableException` and assertions
  that validate you’ve used this correctly!

- Debadree [added support for generating LIR ops where they are pretty
  straightforward boilerplate](https://bugzilla.mozilla.org/show_bug.cgi?id=1916359).
  This will reduce the amount of work we have to do when improving the JITs. Previous
  work like this has been appreciated hugely, and I expect we’ll find lots of value
  in this quickly.

- Denis [has made `--enable-perf` the default for nightly
  builds](https://bugzilla.mozilla.org/show_bug.cgi?id=1918139), which will make
  profiling Firefox more effective.

- A few years ago our OS integration team deployed a neat trick on Windows to reduce
  out of memory errors in Firefox, [written up in this wonderful blog
  post](https://hacks.mozilla.org/2022/11/improving-firefox-stability-with-this-one-weird-trick/).
  Recently [Jon went through the effort to apply this same weird trick to our own
  memory allocation](https://bugzilla.mozilla.org/show_bug.cgi?id=1786451). Our hope
  is that this will reduce the amount of out-of-memory failures.

