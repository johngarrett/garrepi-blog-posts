---
title: The iPhone 3GS in 2020
date: 10/13/2020
abstract: Is an iPhone 3GS a viable option for a daily driver anymore?
tags: technology, vintage computing, ios
---

## Why

Every so often, I find myself not in control of my phone. That is, I spend hours a day aimlessly scrolling through social media while avoiding important calls, texts, and work. I've tried many solutions: turning off biometrics, rigid screen time limits, having my roomate be the sole propiter of my passcode, etc. Having a \$1,000 iPhone makes me want to use it as a \$1,000 iPhone. All of that's besides the point. The solution, for me, is to periododically downgrade to an older iPhone every few months in an attempt to break my habbits. 


In the past, my go to phone was an iPhone 5. With the surplus of phones I have, I decided to go a little further back this time.

![phones]()
> the phones in question

## Form Factor

I won't belabor this, we all know what an iPhone 3GS looks like. It's round and plastic, the screen only has a handful of pixels and is recessed under 20 layers of laminent, and there's only one rear camera (with no flash). The stark constrast between this and the iPhone 4 never fails to amaze me. I can't think of a parallel leap, in the span of a year, that compares.

This phone rocks on a surface, way worse than any iPhone >6 with a camera bump does. 

The 30 pin connector is a thorn I forgot about. I had to venture out to walmart to buy a cable -- coinsidently, there was a tornado passing through GA at the time.

## Setup

### Step 1 -- SIM Card

With my iPhone X, I'm using a nano sim; the 3GS uses a standard SIM. To facilitate this, we have to jump back 3 generations, nano -> micro -> standard. Here's the end result.

[sim card]()

### Step 2 -- restoring

Restoring from a brand new iOS 6.1.6 IPSW was dead simple on MacOS 11. Plug it in, hit restore, done. 

### Step 3 -- Setup

iOS 6 was the first iOS that was "Computer Free"; you didn't need a computer with iTunes to guide you through the setup process. I wish this weren't the case, however, because iCloud signin is effectively broken. This predates the two-factor authentication mandate iCloud now enforces. Easy enough, I'll just have to generate a one-time password. This does not work. The setup asserts `incorrect Apple ID or password` no matter the state. Creating a new iCloud on device doesn't work, resetting the password doesn't work, etc. Fine, no iCloud. 

### Step 4 -- iMessage

iCloud login, with the one-time passcode, works here for some reason. Sweet, iMessage works. I can send and recieve iMessages (the main reason I'm downgrading to another iPhone and not an Android or Blackberry).

### Step 5 -- SpringBoard

Now that we're on the SpringBoard and setup is done, what can we do? Well without iCloud I don't have any contacts so that sucks.

Even if iCloud worked, all my Notes aren't compadible with devices <iOS 9. 

The weather and stock apps don't work either, I'm assuming their REST APIs have changed.

## Jailbreaking

App Store login doesn't work either. Even if it did, iOS 6 doesn't let you download older versions of apps that are compadbile with your phone (I believe iOS 7+ does). Fine, I'll jailbreak it.

We have two options: `p0sixpwn` and `H3lix`

### `H3lix` on a white MBP

doesn't work. probably an itunes issue. whatever

### `p0sixpwn` on Windows 10

doesn't work. iTunes issue. After not using windows for \~5 years, digging through Device Manager and the Services pane were not gentle reintroductions. whatever

### `h3lix` fork on MacOS 11

since we can't run 32 bit apps on mac's anymore, someone forked it and got it compiled to 64 bit. Only compabdilbe with ios 6.1.5, whatever

### `h3lix` on a different macbook pro

it works. sweet. it's an untetherd jailbreak so hopefully I'll never have to deal with this again.

## Camera

it's really not even work discussing the camera -- it sucks; I expected no less. Here are some sample shots.

## Apps

No App Store means no apps, I had to use this [website](). Spotify, Twitter, Instagram, Youtube, and GroupMe are hosted here. Perfect. 

#### Instagram 
it works suprisingly well. videos dont play, direct messages don't load, and there aren't any stories. But the original idea of instagram, a feed of square photos, works.

#### Twitter
twitter also works suprisingly well! It's slow but that's what I want. The friction prevents me from idly checking it. If I _need_ to check twitter, I can.

#### Spotify

I can log in but can't do much else. I suspect another API change. It hangs on this loading screen

[spotify]()

## Communication

What this is all about -- can I text and call? Kind of. 

### iMessage

I dont have any contacts so group chats don't really make any sense to me.

[group chats]()

There aren't any fancy features like reactions or thread replies but that's not a big deal. On the basic level, I can send and recieve photos, emojis, and messages suprisingly well.

### Phone

The phone works great. Making and answering calls is what this phone is best at. It's more comfertable to use than a modern iPhone and it doesn't get into those weird state battles modern iOS does.

### 3G

3G in Atlanta does not work anymore. Maybe it's an issue with the MVNO service plan I use, tracphone. So iMessage doesn't work when I leave the house... that's my 1 (one) requirement.

[4g not working]()


### Text & call forwarding 

I've gotten too used to all SMS texts and calls forwarding to my laptop and iPad. When I'm in the house, my phone is usually off somewhere but calls and texts still make their way to me via my iPad or laptop. I forgot this wasn't a thing pre iOS 9 and ended up missing 4 phone calls yesterday... oops.

## Conclusion

It's fun using this phone. I like the way it feels in my hand and how it fits my pockets. Making phone calls feels cozy and responding to messages is unobtrusive. 

The fundamental issues that prevent me from sticking on this device are:

- the camera
    - i enjoy photography in my spare time
    
- no 3g
    - no data ~= no mobility 

- no contacts

- no apps

Next up, I'll try an iPhone 4 with iOS 7 and see if that fixes any of these problems.
