---
title: The iPhone 3GS in 2020
date: 10/13/2020
image_url: /blog-posts/images/iphone3gs-in-2020/3gs-glamor-shot.jpg
abstract: Is an iPhone 3GS a viable option for a daily driver anymore?
tags: technology, vintage computing, ios
---

![glamor-shot](/blog-posts/images/iphone3gs-in-2020/3gs-glamor-shot.jpg)

## Why

Every so often, I find myself unable to control phone usage. That is, I spend hours a day aimlessly scrolling through social media while avoiding important calls, texts, and work. 
I've tried different solutions:
turning off biometrics to slow down login,
rigid screen time limits,
having my roommate be the sole proprietor of my passcode,
etc. 

At the end of the day, having a \$1,000 iPhone makes me want to use it as a \$1,000 iPhone...

All of that's beside the point. The solution, for me, is to periodically downgrade to an older iPhone every few months. This both breaks my bad habits and curbs my desire for buying a new phone.

In the past, my go to phone was an iPhone 5. With the surplus of phones I have, I decided to go a little further back this time.

![phone-collection](/blog-posts/images/iphone3gs-in-2020/phone-stack.jpg)

> the phone shelf

## Addendum

Reddit user [daniel_dunphy](https://www.reddit.com/user/daniel_dunphy) gave me some great tips that nullify some complaints I bring up later on.

1. signing in with iCloud

The `App Specific Password` route I went wasn't the right thing to do. Instead, you [combine your password with the verification code generated on another device](https://support.apple.com/en-us/HT204974). I remembered doing this before! For some reason, I couldn't find that solution this time around.

2. Downloading older apps

iOS 6 _does_ let you download older app versions from the App Store, like I suspected, *but* you have to have downloaded that app previously with your Apple Id. 

To download a compatible version, you sign into the App Store, navigate to the "Purchased" section and download the apps from there. 


Given this, a lot of my iCloud & app related issues below are no longer valid. Keep that in mind if you're considering to do this yourself.

## Form Factor

I won't belabor this, we all remember what an iPhone 3GS looks like. It's round and plastic, the screen has a handful of pixels while being recessed under 20 layers of laminate, and there's only one rear camera (with no flash).

The stark contrast between this and the iPhone 4 never fails to amaze me though. I can't think of a parallel leap, in the span of a year, that compares.

![iphone4-vs-3gs](/blog-posts/images/iphone3gs-in-2020/3gs-and-4.jpg)

This phone wobbles when using it on a surface, way more than any iPhone with a camera bump. 

The 30 pin connector is a thorn my rose tinted glasses obscured. It's cumbersome, uni-directional, and feels like inserting RAM into a motherboard every time you plug it in.

## Setup

### Step 1 -- SIM Card

With my iPhone X, I'm have a nano sim; the 3GS uses a standard SIM. To use my current phone plan, we have to jump back 3 generations, nano -> micro -> standard. Some electrical tape and scissors managed to do the trick.

![sim-card](/blog-posts/images/iphone3gs-in-2020/sim-card.jpg)

### Step 2 -- restoring

Restoring from a brand new iOS 6.1.6 IPSW was dead simple on MacOS 11. Plug it in, hit restore, done. 

![finder](/blog-posts/images/iphone3gs-in-2020/finder.png)

### Step 3 -- Setup

iOS 5 was the first iOS that was "Computer Free"; you didn't need a computer with iTunes to guide you through the setup process. I wish this weren't the case, however, because iCloud signin is effectively broken. 

This OS predates the two-factor authentication mandate that iCloud now enforces. Easy enough, I'll just have to generate a one-time, app specific, password. No dice. The setup asserts `incorrect Apple ID or password` no matter what you try. Creating a new iCloud on device doesn't work, resetting the password doesn't work, etc. Fine, no iCloud. 

### Step 4 -- iMessage

iCloud login, with that one-time passcode, works here for some reason. Sweet, I can send and receive iMessages (the main reason I'm downgrading to an _iPhone_ and not an Android or Blackberry).

### Step 5 -- SpringBoard

Now that `Setup.app` has run its course, what can we do? Well without iCloud I don't have any contacts so I don't know who to call or text.

All my notes in iCloud aren't compatible with devices below iOS 9 (even if I could sync them).

The weather and stock apps don't work either, I'm assuming their REST APIs have changed.

![maps](/blog-posts/images/iphone3gs-in-2020/apple-maps.PNG)

> Apple Maps works as well as it ever has, down to the turn by turn navigation.

All the other preloaded apps work. Mail, Camera, Reminders, etc.

## Jailbreaking

_I'm including all this because I believe it's a crucial step to using these older iPhones in the modern day._

App Store login doesn't work either. Even if it did, iOS 6 doesn't let you download older versions of apps that are compatible with your phone (I believe iOS 7+ does). Fine, I'll jailbreak it.


Here are my options: 

- [p0sixpwn](https://ih8sn0w.com/p0sixspwn.html)

- [H3lix](https://www.theiphonewiki.com/wiki/H3lix)

- [evasi0n](https://www.theiphonewiki.com/wiki/Evasi0n)


None of these work on MacOS 11, of course, because of lack of 32 bit support.

Linux support is out of the question as well.

### `p0sixpwn` and `evasi0n` on a 2009 MacBook Pro

Neither of these worked. `p0sixpwn` couldn't find the device and `evasi0n` just caused the phone to reboot and nothing more. This is most likely an iTunes issue but with the age of the laptop, I was stuck.

### `p0sixpwn` on Windows 10

Next I switched to my roommates desktop running Windows 10 -- still nothing. `p0sixpwn` couldn't recognize the device. This was another iTunes issue.

I installed a couple versions of iTunes but, no matter what I did, the iPhone wasn't showing up.

After not using windows for \~5 years, digging through Device Manager and the Windows Services Manager were not gentle reintroductions. I threw in the towel.

### `p0sixpwn` fork on MacOS 11

Without 32 bit support, the normal version of `p0sixpwn` doesn't run on MacOS 11. There is, however, a [fork](https://www.reddit.com/r/LegacyJailbreak/comments/fl7u0j/discussion_finally_got_p0sixspwn_to_build_on/) that does. Great.

It opens and detects my device but it's forked from an older base -- it only supports up to iOS 6.1.5...

### `p0sixpwn` on a 2012 MBP running Yosemite 

This was the goldilocks setup. I don't know why and I honestly don't care why -- this is an untethered jailbreak so hopefully I'll never have to deal with this again.

## Camera

The camera isn't really work discussing -- it sucks; I expected no less.

Here are some sample shots:

![georgia-tech](/blog-posts/images/iphone3gs-in-2020/ga-tech.JPG)

> This smudge is _below_ the plastic lense

![another-georgia-tech-shot](/blog-posts/images/iphone3gs-in-2020/gatechx2.JPG)

![room-at-noon](/blog-posts/images/iphone3gs-in-2020/room-nolight.JPG)

![room-window](/blog-posts/images/iphone3gs-in-2020/room-window.JPG)

## The Web

With all the javascript, jquery, and CSS 3 stuff on the modern web, most websites don't work. Those that do take a ludacris amount of time to load.

This website, notably, does work.

![garrepi-blog](/blog-posts/images/iphone3gs-in-2020/garrepi-dev.jpg)

## Apps

No App Store means no apps, I had to use this [mtmdev](https://mtmdev.org/webapp) to download them. Spotify, Twitter, Instagram, Youtube, and GroupMe are hosted here. Perfect. 

![mtmdev](/blog-posts/images/iphone3gs-in-2020/mtmdev.PNG)

#### Instagram 

Instagram works surprisingly well. Videos don't play, direct messages don't load, and there aren't any stories *but* the original idea of Instagram, a feed of square photos, works.

![instagram](/blog-posts/images/iphone3gs-in-2020/instagram.PNG)

#### Twitter

Twitter works the best out of these apps! It's slow but that's what I want. The friction prevents me from idly checking it. If I _need_ to check twitter, I can.

![twitter](/blog-posts/images/iphone3gs-in-2020/twitter.PNG)

#### Spotify

I can log in but can't do much else. I suspect another API change. It hangs on this loading screen. Bummer.

![spotify](/blog-posts/images/iphone3gs-in-2020/spotify.PNG)

## Communication

What this is all about -- can I text and call? Kind of. 

### iMessage

I don't have any contacts so group chats are basically indistinguishable.

![group-chats](/blog-posts/images/iphone3gs-in-2020/messages.PNG)

There aren't any fancy features like reactions or thread replies but that's not a big deal. On the basic level, I can send and receive photos, emojis, and messages surprisingly well.

### Phone

The phone app works great. Making and answering calls is what this phone is best at. It's more comfortable to use than a modern iPhone and it doesn't get into those weird states modern iPhones do.

### 3G

3G in Atlanta seems to be shutoff or maybe there's an issue with the MVNO service plan I use, tracphone. Either way, iMessage doesn't work when I leave the house... that's my 1 (one) requirement of a daily driver.

![no-3g-data](/blog-posts/images/iphone3gs-in-2020/no-data.PNG)


### Text & call forwarding 

I've gotten used to all SMS texts and calls forwarding to my laptop and iPad. When I'm at home, my phone is usually off somewhere but calls and texts still propagate to me via my iPad or Mac. I forgot that feature was introduced in iOS 9 and ended up missing a couple important calls yesterday... oops.

![missed-calls](/blog-posts/images/iphone3gs-in-2020/notifications.PNG)

## Conclusion

It's fun using this phone. I like the way it feels in my hand and how it fits my pockets. Making phone calls feels cozy and responding to messages is unobtrusive. 

The fundamental issues that prevent me from sticking on this device are:

- no 3g: no data ~= no mobility 
- the camera
- the lack of apps
- no contacts

I really wanted to use this phone but with all these hurdles, I can't.

I'll try and setup an iPhone 4 on iOS 7 and see if that smooths any of these pain points.
