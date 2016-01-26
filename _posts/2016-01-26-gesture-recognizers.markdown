---
layout: post
title:  "Simple gesture recognition"
date:   2016-01-26 09:44:31
categories: ios swift xcode
tags: [ios, swift, xcode, gestures]
---

You can easily add gesture recognition to your app either by dragging and dropping a recognizer onto your storyboard or entirely through code.

### Using storyboard

Let's say we have an image on our storyboard and we want to know when the user taps it. To accomplish this we need to add a tap gesture recognizer to our image view:

![Add tap gesture recognizer](/images/2016-01-26/add-tap-gesture.gif)

Make sure you drop it on the image itself, not on the view around it.

Almost done! Create an `@IBAction` for your new gesture recognizer:

![Create an @IBAction](/images/2016-01-26/create-ibaction.gif)

Now, the last and a **very important** part (which I keep forgetting to do myself): enable "User Interactions Enabled" on your image:

![Enable user interactions](/images/2016-01-26/enable-interactions.png)

And we're in business!

![Taps!](/images/2016-01-26/demo-1.gif)

### Recognizers using only code

Storyboards are great but sometimes you can't achieve what you want with them (or you don't like them).

Adding a gesture recognizer from code is fairly easy. Let's do the same thing we did in the previous section.

Here's all the code that you need to do that is here, commented.

<script src="https://gist.github.com/diversario/bf9ebf6a1c92ed95b3a9.js"></script>

It works just like the previous example!
