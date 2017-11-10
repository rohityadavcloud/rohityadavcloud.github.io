---
redirect_from: "/logs/"
layout: default
---

<ul class="recent-posts">
  {% for post in site.posts %}
    <li>
      <div>
        <span class="title"><a href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a> <span class="label label-{{ post.highlight }}"><a href="/{{ post.category }}" style="color: #fff; text-decoration: none;">{{ post.category }}</a></span></span>
        <span class="date">{{ post.date | date: "%b %-d, %Y" }}</span>
      </div>
    </li>
  {% endfor %}
</ul>
