---
redirect_from: "/logs/"
layout: default
---

<ul class="recent-posts">
  {% for post in site.posts %}
    <li>
      <div>
        <span class="title"><a href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a></span>
        <span class="date">{{ post.date | date: "%b %-d, %Y" }}</span>
      </div>
    </li>
  {% endfor %}
</ul>
