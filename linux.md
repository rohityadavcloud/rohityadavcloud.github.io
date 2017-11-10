---
layout: default
---

<h3>Archives for Linux</h3>
<ul class="recent-posts">
  {% for post in site.posts %}
  {% if post.category == "linux" %}
    <li>
      <div>
        <span class="title"><a href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a> <span class="label label-{{ post.highlight }}"><a href="/{{ post.category }}" style="color: #fff; text-decoration: none;">{{ post.category }}</a></span></span>
        <span class="date">{{ post.date | date: "%b %-d, %Y" }}</span>
      </div>
    </li>
  {% endif %}
  {% endfor %}
</ul>
