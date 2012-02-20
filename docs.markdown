---
layout: default
title: Docs
permalink: /docs/
---

{% capture cmds %}{% include cmds.markdown %}{% endcapture %}
{% capture apache %}{% include apache.markdown %}{% endcapture %}
{% capture bind9 %}{% include bind9.markdown %}{% endcapture %}

<h2><a href="{{page.url}}">Docs »</a></h2>
<ul class="nav nav-tabs">
  <li class="active"><a href="#cmds" data-toggle="tab">General</a></li>
  <li data-dropdown="dropdown" class="dropdown">
    <a href="#" class="dropdown-toggle" data-toggle="dropdown">Services <b class="caret"></b></a>
    <ul class="dropdown-menu">
      <li><a href="#apache" data-toggle="tab">Apache</a></li>
      <li><a href="#bind9" data-toggle="tab">Bind9</a></li>
    </ul>
  </li>
</ul>

<div class="tab-content" id="my-tab-content">
  <div id="cmds" class="active tab-pane">
    {{ cmds | markdownify }}
  </div>
  <div id="apache" class="tab-pane">
    {{ apache | markdownify }}
  </div>
  <div id="bind9" class="tab-pane">
    {{ bind9 | markdownify }}
  </div>
</div>

<script>
  $(function () {
    $('.tabs a:last').tab('show')
  })
</script>
