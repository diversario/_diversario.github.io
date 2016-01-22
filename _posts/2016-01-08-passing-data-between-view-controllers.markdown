---
layout: post
title:  "Passing data between view controllers"
date:   2016-01-22 12:04:23
categories: ios swift xcode
tags: [ios, swift, xcode]
---
The first time I tried to use a table view this problem came up almost immediately: how do I tell the detail view which cell I tapped on? As it turned out, there are several ways of doing this and it doesn’t matter if you’re using a table view or a simple button.

### Storyboard segues

This is an easy and straightforward way of passing data around using hooks provided by the SDK.

We’ll be using two methods that UIViewController provides to work with segues: `performSegueWithIdentifier:id:sender` and `prepareForSegue:segue:sender`. But first, we need to create the segue itself (duh 😉):

![]()

Jekyll also offers powerful support for code snippets:

{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

Check out the [Jekyll docs][jekyll] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll’s dedicated Help repository][jekyll-help].

[jekyll]:      http://jekyllrb.com
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-help]: https://github.com/jekyll/jekyll-help
