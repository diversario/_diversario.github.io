---
title:  "What I Learned About SpriteKit, Part I"
date:   2016-05-13 22:12:01
categories: [ios]
tags: [ios, spritekit, game development]
---
I got into SpriteKit wanting to make a 2D game. A very simple game, in fact — you could play it with one finger 👆! I watched the WWDC SpriteKit demo and it seemed to suite my simple needs perfectly.

I’m sure pretty much any developer who made or tried to make a game is very familiar with the issues I ran into but for me — all this was new and unexpected.

## Physics

Using physics simulation for collisions, contact detection and so on sounds like a great idea in theory and seems like a timesaver. That is, until you try to make things work the way you want.

### collisions

In my game, I decided to use collision detection to make player interact with obstacles in a realistic way. The idea was: player lands on an obstacle and in a collision handler I attach the player to that obstacle so it appears as if the player is sitting on the obstacle:

![Pardon my scribbles](/images/2016-05-13/collision.png)

It is such a simple want. Or so I thought, until I realized that this is never going to happen if I rely purely on collision detection.

SpriteKit physics engine (and many other game engines) performs collision detection at fixed intervals or steps. By default, SpriteKit performs collision detection once per frame. Say we have something like this:

![Slow speed collision](/images/2016-05-13/slow-speed-collision.png)

At low speeds, collision detection works well. However, if you have a fast moving physics body collisions aren’t registered as expected:

![High speed collision](/images/2016-05-13/high-speed-collision.png)

Between frames 2 and 3 the smaller body moved inside the larger one and at that point collision was detected. SpriteKit documentation informs us that

> If two bodies in a collision do not perform precise collision detection, and one passes completely through the other in a single frame, no collision is detected.

I guess I should be using precise collision detection?.. Let’s try that!

### Precise Collision Detection

Physics bodies have a property [`usePreciseCollisionDetection`](https://developer.apple.com/library/ios/documentation/SpriteKit/Reference/SKPhysicsBody_Ref/index.html#//apple_ref/occ/instp/SKPhysicsBody/usesPreciseCollisionDetection):

> If this property is set to true on either body, the simulation performs a more precise and more expensive calculation to detect these collisions. This property should be set to true on small, fast moving bodies.

This seemed to be exactly what I needed. I set it to true and ran the game. There was an improvement, but not a major one — player was getting stuck in obstacles here and there and collisions weren’t consistent.

After implementing a terrible hack I sort of got around that – it was good enough for me to continue onto other pieces of the game. But I got immediately blocked by a bizarre and unexplainable bug: when testing for **contact** (not collision) between player and a hazard player would get stuck in place for about a second _only while being attached to an obstacle_ (it’s kind of hard to explain clearly without a video, which I don’t have).

In other words, if you sit on a moving platform and you hit a hazard — you’ll be stuck in place for a bit while the platform moves under you. I asked about this twice ([here](https://forums.developer.apple.com/message/116547#116547) and [there](https://forums.developer.apple.com/message/118420#118420)) on Apple developer forums but got no reply.

I resorted to another workaround which worked well but I couldn’t find an explanation for the bug I saw. About a week ago, I stumbled on a solution: disable precise collisions (so simple, why didn’t I disable them earlier? I don’t know). I’m fairly sure this is why bodies were getting stuck.

I found that if you have physics bodies connected via [joint](https://developer.apple.com/library/ios/documentation/SpriteKit/Reference/SKPhysicsJoint_Ref/index.html#//apple_ref/swift/cl/c:objc%28cs%29SKPhysicsJoint) and at least one of the bodies uses precise collision detection, then **contact** detection incurs a massive performance penalty. Curiously, if there is no joint this does not happen. This might be due to the fact that two bodies in a joint are treated as one and the larger, combined physics body then uses precise collision detection. I don’t know for sure.

In the end, I was able to achieve the desired behavior by using contact detection only, without precise collision detection, and implementing an algorithm that adjusts player position to appear in the correct spot once contact happens:

{% highlight text %}
onContact:
  get player position
  get platform position
  calculate distance between player and platform edge
  move player to platform edge
 {% endhighlight %}

As far as workarounds go, this wasn’t that hard and this actually works 🙌

### Physics and screen size

In my game, player can jump. Jumping is done by applying an [impulse](https://developer.apple.com/library/ios/documentation/SpriteKit/Reference/SKPhysicsBody_Ref/#//apple_ref/occ/instm/SKPhysicsBody/applyImpulse:) to the player in appropriate direction. Physics, including gravity, takes care of the rest. I was testing the gameplay on iPhone 4 simulator – I figured, I would target the smallest device and the game would naturally scale to the rest of them.

**Wrong.**

The very first thing I noticed when running the same code on an iPhone 6 Plus is how player wasn’t able to jump as high or as far as I expected. After doing some research, I came across a host of similar issues reported by developers using SpriteKit. Turns out, forces don’t automatically adjust to all screen sizes.

SpriteKit physics operates in metric units. Physics bodies have properties like mass and density defined in kilograms and used in simulation. How does this relate to screen size? Somewhere behind the scenes metric units are translated into screen pixels – for example, when a body is moving as a result of applying an impulse to it.

An impulse _**I**_ applied to a body of mass _**M**_ will move the body by _**X**_ pixels, on any screen. Performing this on iPhone 4 might move the body across the screen but only 2/3 of the way on an iPhone 6 Plus. So how can we make these forces consistent?

Apple dev forums have several threads like [this](https://forums.developer.apple.com/message/79553#79553) and [this](https://forums.developer.apple.com/message/89572#89572) where developers are sharing experience about making forces scale to screen. As far as I can tell, the current consensus is that 150px equal to 1m. In theory, this should allow you to find a multiplier that you can apply to all forces. In practice, I have this code to calculate an impulse multiplier that launches player across the screen in a parabolic curve:

<script src="https://gist.github.com/diversario/a2920581648c8bcee59ff8cb2d62d2d5.js"></script>

This takes player from one side of the screen and lands it on the opposite side at (hopefully) the same _**x**_ coordinate.

`SCREEN_SCALE` value is also used as a multiplier for player mass and world gravity. You must scale all forces to achieve consistency.

I can honestly say that this is one the most ridiculous thing I had to do to make something work that _should already be working_. I understand that always scaling physics to fit the screen is undesirable (i.e., if you want iPad version to display larger area) but I feel like this should be a setting, not a magic number that you have to dig up on the internet.

---

In its current state, physics in my game is used sparingly and a lot of things are faked. It is a great learning experience, though, and it applies to a lot (if not all) of games out there that use physics simulation:

<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">Is it just me, or do a lot of folks who jump straight into the industry via Unity 3D never seem to grasp how much of AAA games are faked?</p>&mdash; Megan Fox (@glassbottommeg) <a href="https://twitter.com/glassbottommeg/status/710847380988428289">March 18, 2016</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>
