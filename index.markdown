---
layout: default
title: Home
tagline: Writings, essays and blog posts
permalink: /
---

<div class="row">
  <div class="span7">
    <table class="table table-striped table-condensed">
      <tbody>
      {% for post in site.posts %}
        <tr>
          <td style="padding-top: 8px; width: 90px;">
          <a href="{{ post.url }}"><span class="badge badge-info">{{ post.date | date: "%b %e, %Y" }}</span></a>
          </td>
          <td><h3><a href="{{ post.url }}">{{ post.title }}</a> <small>{{ post.excerpt }}...</small></h3></td>
        </tr>
      {% endfor %}
      </tbody>
    </table>
  </div>
  <div class="span3" style="margin-left: 0; padding-left: 19px;border-left: 1px solid #eee;">
    <div id="recentcomments" class="dsq-widget"><h3 class="dsq-widget-title">Recent Comments</h3><script type="text/javascript" src="http://yadavium.disqus.com/recent_comments_widget.js?num_items=5&hide_avatars=0&avatar_size=32&excerpt_length=128"></script></div>
  </div>
</div>


