---
layout: post
title:  "The History of the sockpp library"
date:   2023-12-26 10:47:43 -0400
categories: sockpp, C++
---
I had just gotten back to software consulting in the early 2000's when the aftermath of the Dotcom crash was really settling in. It was tough to find free-lance work, and when you did, a two year contract would often get cancelled within the first week as budgets got cut. ("Hello, welcome. Now go home.") So I retreated up to the mountains for a while and just started hacking on some stuff.

At the time, I was primarily working on medical and biotech devices that sat on a desktop next to a PC and were controlled through a Windows GUI on the PC. To communicate with the device, legacy connections like serial and parallel printer ports were being phased out (and much too slow anyway), and although USB was well established, there wasn't a quick, cheap, out-of-the-box solution to get a prototype up and running quickly. So Ethernet was the go to choice to get started. With C++ as my language for implementing these devices, due to its rather paltry standard library, it seemed like 80% of the job was just getting a base embedded library stood up to implement the applications. And, since the next job was at a different company, I had to start all over again from scrath each time.

So I started writing that 80% on my own nickel, as my own property. I called it _The Controller Framework_. ("Frameworks" were big in the 90's). It had portable threading clases that ran on Windows, Linux, and a number of popular Real-Time Operating Systems; binary I/O streams; an expanded string class; and communications code for legacy ports, some USB, and of course, TCP/IP networking. The idea at the time was not to open-source the library, or sell it directly, but rather to use it in a sales pitch to potential new customers: If you hire me, the project will already be half way done when I start, and that half will already be well tested and debugged.

The idea worked. I picked up numerous jobs and implemented them with _CtrlrFx:_ digital research microscopes, wind power equipment, a few industrial controllers, and data some loggers.

But soon the new extensions for C++11 made most of the library moot. I planned to create a new version of _CtrlrFx_ that used `std::thread` and related classes, but it never happened. The framework didn't seem to offer a lot that wasn't available as part of the standard, in `boost`, or other open-source libraries. But it did have a bunch of classes for simple networking using an RAII _acceptor/connector_ pattern. I thought those might be useful.

The one thing that bugged me about using the `socket` classes in the original C++03 version of my library was their usage in a multithreaded environment. There wasn't an easy way to send a socket from one thread to another. The C++ socket objects could not be copied, because you don't generally want to share the same file handle across objects. So to send a socket to another thread, you had to `release()` the underlying integer file descriptor from the object, send _that_ to the other thread, and reconstruct the object on the other side. This worked but it was ugly and prone to resource leaks. People would use the sometimes use the socket descriptor directly and forget to close it.

But C++11 had an elegant solution, so I extracted the socket classes from the framework, created a new library called **sockpp**, and used it to learn about C++ _move semantics._ It worked quite nicely. Making a `socket` moveable (but not copyable) fixed the ugliness and resource leaks.

So I put the library in a drawer and forgot about it for years!

Around 2016, I was working on a biotech robotic vision system and needed to write some test apps for a network motor controller. I remembered my lost library. Since these were throw-away tests that weren't destined for production code, I didn't bother with any legalese for the source. The tests worked and I moved on to the next job at a different company. A few months later, I got a call from a manager at the old place (hey, Keith!). They wound up using the library for production, and now the lawyers were asking about licensing. I was really busy working on a CAT scanner, so I told him I would open-source the project with a free license and make it public.

So I slapped a BSD license on the code, put it up on GitHub, and... forgot about for years.

By this time, I had mostly switched to Rust for work and didn't have much use for C++ libraries, so _sockpp_ just sat unloved. Until someone found it. Out of nowhere, it started getting bug reports. Then stars. Then pull requests! It definitely caught me by surprise. I begrudgingly started fixing some of the bugs.

I'm still mostly working with Rust professionally, but I'm sure I'm going to need a C++ socket library at some point down the road. So I keep at it. Some of the folks sending in comments and contributions have helped keep me up to date with the evolving C++ standards, and have taught me a lot. It has made C++ fun again.

So almost exactly 20 years after the initial lines were written, _sockpp_ reached a v1.0 milestone. That felt good.

...so to celebrate, I immediately startd working on _sockpp_ v2.0.

















