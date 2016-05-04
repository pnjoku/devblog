---
layout: post
title:  "Asynchronicity with Gulp and RunSequence"
author: "Jessica Kennedy"
date:   2016-05-03 13:14:47 -0400
categories: gulp, build tools, async, node
---

I was working today on a very simple `gulp` build. Essentially, the build was initially cleaning, then building the app out (just js & html) into a distribution directory. Something like this:

{% highlight javascript %}
gulp.task('build', ['clean'], function(){
  runSequence(['build:js', 'build:html']);
})

gulp.task('build:js', ...);
gulp.task('build:html', ...);

gulp.task('clean', ...);
{% endhighlight %}

Everything was going smoothly until I added testing in to the mix.
{% highlight javascript %}
gulp.task('test', ['build'], ...);
{% endhighlight %}

Looks like it would work fine, right? Well, it certainly *appeared* to work fine, but none of my tests were running. Upon inspecting the console, I noticed something I didn't expect. Here is something like what I saw:
```
[10:27:48] Starting 'clean'...
[10:27:48] Finished 'clean' after 1.76 ms
[10:27:48] Starting 'build'...
[10:27:48] Starting 'build:js'...
[10:27:48] Starting 'build:html'...
[10:27:48] Finished 'build' after 13 ms
[10:27:48] Starting 'test'...
[10:27:48] Finished 'test' after 42 μs
[10:27:48] Finished 'build:html' after 220 ms
[10:27:48] Finished 'build:js' after 232 ms
```

Notice something off?  looks like `build:html` & `build:js` finished *After* my initial `build` process, *and* after the `test` had run. Boo! Apparently the tasks don't wait for `runSequence` to complete by default. Luckily, it's a super simple fix to make them wait- just add a `callback` at the end of `runSequence` calls:

{% highlight javascript %}
gulp.task('build', ['clean'], function(callback){
  runSequence(['build:js', 'build:html'], callback);
})

gulp.task('build:js', ...);
gulp.task('build:html', ...);

gulp.task('clean', ...);
{% endhighlight %}

Now our build is behaving as expected:
```
[10:57:27] Starting 'build'...
[10:57:27] Starting 'clean'...
[10:57:27] Finished 'clean' after 11 ms
[10:57:27] Starting 'build:js'...
[10:57:27] Starting 'build:html'...
[10:57:27] Finished 'build:html' after 232 ms
[10:57:27] Finished 'build:js' after 244 ms
[10:57:27] Finished 'build' after 256 ms
[10:57:27] Starting 'test'...
```

Of course, this is all covered in the `runSequence` documentation, but if you develop like me, you start with what you need and reference the documentation as you go, so hopefully this saves you some heartache!