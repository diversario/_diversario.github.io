---
layout: post
title:  "Passing data between view controllers"
date:   2016-01-22 19:38:23
categories: ios swift xcode
tags: [ios, swift, xcode, viewcontroller, segue]
---
The first time I tried to use a table view this problem came up almost immediately: how do I tell the detail view which cell I tapped on? As it turned out, there are several ways of doing this and it doesn’t matter if you’re using a table view or a simple button.

### Storyboard segues

This is an easy and straightforward way of passing data around using hooks provided by the SDK.

We’ll be using two methods that UIViewController provides to work with segues: `performSegueWithIdentifier:id:sender` and `prepareForSegue:segue:sender`. But first, we need to create the segue itself (duh 😉):

![Creating a segue.](/images/2018-01-22/make-segue.gif)

All you need to do is drag from your source view controller into the destination one; make sure to start dragging from the yellow circle icon at in the header of the view controller. Select **Show** or **Show Detail** as segue type. The difference between the two is how they’re presented: **Show** will slide up from the bottom of the screen while **Show Detail** will slide from the right to left. You should see an arrow with a circle on it appear between the two view controllers.

Once the segue is created you need to give it an identifier. Select the segue on the storyboard and go to the **Attributes Inspector - Identifier**:

![Segue identifier](/images/2018-01-22/segue-id.png)

We got our segue! Now, the fun part.

What we’re going to do is write some code to run just before the destination view controller becomes active and we’ll initialize a member on it with the data we want it to have. Of course, for this to work we need to have the appropriate member on the destination controller:

<script src="https://gist.github.com/diversario/3ee3d9527ca1a9b92e9b.js"></script>

Now, let’s go to our source view controller and set up the two methods we saw in the beginning. In this code we want to transition to the destination view controller when a user taps a cell in a table view:

<script src="https://gist.github.com/diversario/9c3932feb0f668d8a707.js"></script>

We call `performSegueWithIdentifier` in the `tableView` function because it is called when user taps a cell — this can, of course, be any other function — and within it we kick off our segue. The confusingly-named `sender` parameter is how we can pass data along with the segue. Before the segue actually runs the `prepareForSegue` method is called, giving us access to the destination controller (`DestVC`). All we have to do now is set the data on the controller!

### Instantiating a view controller by its identifier

A variation of the first method, this one does not require a segue. The main idea is the same: set data on the destination view controller before it’s loaded. The difference is in the way get the controller.

First, we need to give our destination view controller a storyboard ID:

![Segue identifier](/images/2018-01-22/storyboard-id.png)

Then, just like in the previous method, we add some code to the function from which we want to do the segue:

<script src="https://gist.github.com/diversario/1d7319268eb6ecb305e2.js"></script>

`DestVC` should, of course, have a member for us to set.

### Using a shared state

This is probably my least favorite way of passing data around — globally-accessible mutable state seems like an accident waiting to happen. However, if for some reason the other two methods aren’t applicable to your case then go ahead and use a global object.

The idea is to have a class (or service) with a static member which stores the piece of data that needs to be passed around. The first view assigns a value to the member and the second view reads it.

The class to hold the data can look something like this:

<script src="https://gist.github.com/diversario/8b13777f26a069356547.js"></script>

Imagine how easy it is to screw this up.

Using this is easy as well — I will leave that for you to figure it out. Just be careful.
