---
layout: post
title:  "Automatic font size in UILabel"
date:   2016-01-16 20:36:45
categories: ios
tags: [ios, swift, xcode, label]
---
When you use a `UILabel` to display a variable amount of text sometimes you want it to increase or decrease the font size to either fit more text or fill the label with a short snippet.

You can achieve this by using the **Autoshrink** label property:

![Autoshrink property](/images/2016-01-16/var-font-size.gif)

Here, my 60pt font label is allowed to decrease its font size down to 10pt when the content does not fit into it. And it works:

![Autoshrink property](/images/2016-01-16/rotations.gif)

Easy! 🙌
