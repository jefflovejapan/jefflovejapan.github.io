---
layout: post
title: Learning AV Foundation
tags: [tech book a month]
---

Lately I've been wanting to do more learning outside work hours, so when my coworkers shared their personal goals for 2020 in Slack, I thought, "This is the year I read a technical book a month." Since I work with AVFoundation every day at [1 Second Everyday](https://1se.co), I figured what better way to start than with a book-length treatment of my favorite (?) framework, Bob McCune's [_Learning AV Foundation: A Hands-on Guide to Mastering the AV Foundation Framework_](http://www.learningavfoundation.com). At 432 pages it covers a lot of ground, with walkthroughs on media playback and capture, working with assets and metadata, and composition and editing. It starts from the very basics (how digital media is represented on disk) and works up to more complex topics -- from how to work with rational time to how to control focus and exposure on iPhone cameras, eventually building up to a multitrack editing app by the end of the book.

I love technical books like David Beazley's [_Python Essential Reference_](https://www.dabeaz.com/per.html) -- they explain complex ideas in terse, well-formulated language, give a clear structure and progression, and steer clear of the jokey voice that shows up everywhere in tech books for beginners. I wouldn't put _Learning AV Foundation_ in quite the same tier -- it's a project-based book and not really a reference -- but it was a good read, communicated the main ideas clearly, and provided enough scaffolding that the project work could be really focused on what he's trying to teach in a given chapter. I won't spoil what's in the book, but here are a handful of takeaways from someone who's spent the last 8 months or so working with AV Foundation:

- I had no idea about the atom/box structure of MP4 and QuickTime files. I wish that Apple's Atom Inspector app was updated to run on recent OSes -- please consider filing a Radar referencing [this report](https://openradar.appspot.com/radar?id=5004193051967488) if you'd like the same. Also, any recommendations for other inspection tools for MP4 and MOV?
- The richness of metadata that MP4 and QuickTime can support is really cool. In particular, the idea that you can add dictionaries containing your own structured data to a video file.
- How simple it is to set up AirPlay
- How `AVCaptureSession` works and how you can wire different inputs and outputs up to it

So what's not to like about it?

- All Objective-C
- Some stuff is outdated (published in 2015) and the source code contains a couple of frustrating errors
- Reading code in the iBooks ePUB version is really bad, and pretty typical. I'd recommend a physical copy or PDF if possible.
- I would love to have had a treatment of some higher-level ideas, like dealing with multiple timescales and performance considerations for custom video compositors.

I'd definitely give it a thumbs up overall and would recommend it to people who want a broad overview of the framework. It goes pretty deep in parts (`AVAssetWriter`, `AVVideoCompositionCoreAnimationTool`, `AVCaptureVideoDataOutput`) and always leaves the reader with enough clues to continue digging on their own.
