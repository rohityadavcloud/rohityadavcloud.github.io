---
layout: default
title: Blog
---

<ul class="recent-posts">
  {% for post in site.posts %}
    <li>
      <div>
        <span class="date">{{ post.date | date: "%b %-d, %Y" }} </span>
        <span class="title"><a href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a></span>

      </div>
    </li>
  {% endfor %}
</ul>
