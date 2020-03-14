---
layout: page
title: Blog
redirect_from: "/blog/"
---

<table class="table table-hover">
  <thead>
  </thead>
  <tbody>
  {% for post in site.posts %}
    <tr>
      <td></td>
      <td class="light">{{ post.date | date: "%b %d, %Y" }} </td>
      <td class="title"><a href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a></td>
    </tr>
  {% endfor %}
  </tbody>
</table>
