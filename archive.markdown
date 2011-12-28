---
layout: page
title: Archive
tagline: Writings, essays and blog posts
permalink: /posts/
---

<div class="row">
  <div class="span10">
    <table class="zebra-striped condensed-table">
      <tbody>
      {% for post in site.posts %}
        <tr>
          <td style="padding-top: 12px; width: 120px;"><code style="font-size: 13px;">{{ post.date | date: "%b %e, %Y" }}</code></td>
          <td><h3><a href="{{ post.url }}">{{ post.title }}</a> <small>{{ post.excerpt }}...</small></h3></td>
        </tr>
      {% endfor %}
      </tbody>
    </table>
  </div>
  <div class="span4">
    <div id="recentcomments" class="dsq-widget"><h3 class="dsq-widget-title">Recent Comments</h3><script type="text/javascript" src="http://yadavium.disqus.com/recent_comments_widget.js?num_items=5&hide_avatars=0&avatar_size=32&excerpt_length=128"></script></div>
  </div>
</div>


