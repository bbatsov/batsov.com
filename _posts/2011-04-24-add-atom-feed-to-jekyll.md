---
title: "Add an Atom feed to a Jekyll blog"
tags:
- Jekyll
- Atom
- Tutorials
---

As you know I've recently migrated my blog from WordPress to
Jekyll. One of the things I had to do was add an Atom feed (RSS
sucks). It was quite the easy task. I just had to create an
[atom.xml](https://github.com/bbatsov/blog/blob/master/atom.xml) file
and place it in the root of my blog. Here's source code for the feed:

``` xml
{% raw %}
---
layout: nil
---
<?xml version="1.0"?>
<feed xmlns="http://www.w3.org/2005/Atom">

  <title>www.batsov.com</title>
  <link href="https://batsov.com/"/>
  <link type="application/atom+xml" rel="self" href="https://batsov.com/atom.xml"/>
  <updated>{{ site.time | date_to_xmlschema }}</updated>
  <id>https://batsov.com/</id>
  <author>
    <name>Bozhidar Batsov</name>
    <email>bozhidar@example.org</email>
  </author>

  {% for post in site.posts %}
  <entry>
    <id>https://www.batsov.com{{ post.id }}</id>
    <link type="text/html" rel="alternate" href="https://batsov.com{{ post.url }}"/>
    <title>{{ post.title | xml_escape }}</title>
    <updated>{{ post.date | date_to_xmlschema }}</updated>
    <author>
      <name>Bozhidar Batsov</name>
      <uri>https://batsov.com/</uri>
    </author>
    <content type="html">{{ post.content | xml_escape }}</content>
  </entry>
  {% endfor %}

</feed>
{% endraw %}
```

Basically, we're using Liquid to generate the necessary Atom XML structure. You can easily tweak this code for your own purposes (e.g. you might want to filter out certain posts, etc).

Afterwards I only had to link my default layout to the Atom feed:

``` xml
<link rel="alternate" type="application/atom+xml" href="atom.xml" title="Atom feed">
```

At this point I was able to subscribe to my new Atom feed (and
hopefully my followers, which I may or may not have, were able to do
the same).

**Update:** These days (circa 2021) there are [simpler ways]({% post_url 2021-11-13-atom-feeds-in-jekyll-redux %}) to create Atom feeds in Jekyll.
