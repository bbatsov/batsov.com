---
layout: post
title: Farewell Disqus
date: 2020-12-08 10:50 +0200
tags:
- Disqus
- Hyvor Talk
- Jekyll
---

Just wanted to let you know that for [various reasons](https://fatfrogmedia.com/delete-disqus-comments-wordpress/) I've migrated the comments of `(think)` from
Disqus to Hyvor Talk. I've described the process in a [dedicated article](https://metaredux.com/posts/2020/12/07/migrating-from-disqus-to-hyvor-talk.html).

One small difference in the migration procedure for `(think)` in particular is that because it uses the [Hydeout Jekyll theme](https://github.com/fongandrew/hydeout),
I've opted to change `_includes/comments.html` instead of `_layouts/posts.html`. Here's how it looks now:

``` html
{% raw %}
{% if page.comments != false %}
<section class="comments">
    <div id="hyvor-talk-view"></div>
    <script type="text/javascript">
     var HYVOR_TALK_WEBSITE = YOUR_SITE_ID; // DO NOT CHANGE THIS
     var HYVOR_TALK_CONFIG = {
         url: '{{ page.url | absolute_url }}',
         id: '{{ page.url | absolute_url }}'
     };
    </script>
    <script async type="text/javascript" src="//talk.hyvor.com/web-api/embed"></script>
</section>
{% endif %}
{% endraw %}
```

There are numerous ways to approach this, but that one seemed like the optimal one to me.

The migration process converted all Disqus comments to guest comments in Hyvor, which is a bit unfortunate, but unavoidable.
Still, I think that's a small price to pay for a privacy-focused solution with better usability. I hope that this transition
will result in more interesting discussions taking place here!
