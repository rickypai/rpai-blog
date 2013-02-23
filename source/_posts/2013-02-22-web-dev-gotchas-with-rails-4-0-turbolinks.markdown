---
layout: post
title: "Web Dev: Gotchas with Rails 4.0 Turbolinks"
date: 2013-02-22 16:02
comments: true
categories: [web development, ruby on rails, rails 4.0, gems]
---

My first post regarding web development. As I couldn't sleep last night, I started working on a personal project again.

Rails 4.0 is just around the corner with lots of updates and new features. While it might be premature to upgrade your current application to edge Rails 4.0, a lot its core features have been extracted as gems that work with older versions of Rails.

Some gems have even started migrating to 1.0 version numbers as par [this commit](https://github.com/rails/turbolinks/commit/41dd321407f837d9705de9d893f2847537676904):

> Fuck it, lets just call it 1.0

> dhh authored a month ago

One that I've been using is [turbo-sprockets-rails3](https://github.com/ndbroadbent/turbo-sprockets-rails3), which keeps a digest file for assets so it only compiles the ones that have been changed, dramatically increasing the `asset:precompile` time.

But one that is extremely interesting is [turbolinks](https://github.com/rails/turbolinks). What turbolinks does, according to my understanding, is converting all the links to AJAX requests. When a links is clicked, the targeted page is fetched in the background, and the new page's body and title will replace the current one, effectively skipping the loading of assets. It also uses HTML5 pushState so keep track of the new URL!

Earlier versions seem to cause problems with other AJAX requests, but so far I have not encountered much problems with it.

But there are a few gotchas with this:

1. Place it in `<head>`

    My project was defer loading javascripts with the `<script>` tags placed after the `<body>` tag to reduce perceived load time.
    What I discovered that turbolinks does not work properly when the scripts are defer loaded.

    First, it has a tendency to send out multiple ajax submits as discussed [here](https://github.com/rails/turbolinks/pull/185).
    Second, in some cases, it just won't work (when using Chrome and [retina.js](http://retinajs.com/)).

    The fix is simple, just move the script loading to inside `<head>`.

2. retina.js is not fully supported

    With retina.js, `@2x` assets are loaded during the initial page load. However, with turbolinks, any subsequent page load will not trigger retina.js's callbacks.

    I suspect there the event hook of retina.js can be modified, but for now, I just made my site serve all `@2x` thumbnails with [this patch](https://gist.github.com/rickypai/5017676).

Have fun!