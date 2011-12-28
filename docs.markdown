---
layout: default
title: Docs
permalink: /docs/
---

{% capture cmds %}{% include cmds.markdown %}{% endcapture %}
{% capture apache %}{% include apache.markdown %}{% endcapture %}
{% capture bind9 %}{% include bind9.markdown %}{% endcapture %}

<ul data-tabs="tabs" class="tabs">
  <li><a href="{{page.url}}" data-original-title="Docs describing 'How-To' instructions" data-placement="above" rel="twipsy"><h2>Docs »</h2></a></li>
  <li class="active"><a href="#cmds">General</a></li>
  <li data-dropdown="dropdown" class="dropdown">
    <a class="dropdown-toggle" href="#">Services</a>
    <ul class="dropdown-menu">
      <li><a href="#apache">Apache</a></li>
      <li><a href="#bind9">Bind9</a></li>
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
