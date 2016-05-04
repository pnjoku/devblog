---
layout: post
title:  "Mouse vs Touch Events"
author: "Jessica Kennedy"
date:   2016-05-04 15:53:47 -0400
categories: events, javascript, mobile,
---

I came across a bug today involving a `blur` event on an `input` who had only one job: to remove a class from a dropdown, subsequently hiding the dropdown. My dropdown was being css-animated out, and everything worked great on it when I was using my cutting-edge macbook pro. But then, some users started complaining. Apparently, if they clicked on something at the bottom of the dropdown, the item they clicked on wasn't being recognized. Weird.

Now, I imagine you have jumped to the same conclusion as myself - the `blur` event fires first, then the `click` event, so of course by the time the `click` event registered in the dropdown, the dropdown was already gone, no bubbling happened, blablabla. So why did I never see this issue on my mac? Weirder still, if I plugged a mouse in to my mac, and used it, I could get the same issue to occur sometimes (though less consistently).

Here's what I think was happening:

It's pretty well known that `click` events and `touch` (and the newest `pointer`) events are inconsistent across devices. I believe macs register their `touch` events on the trackpad well before `click` events, which would also account for why machines without a trackpad were seeing the problems more consistently. Lesson learned: `click` events are slower (which we knew), and macs seem to register an actual mouse click event differently than it does on its trackpad.