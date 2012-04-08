---
layout: default
title: Docs
permalink: /docs/
---

{% capture kernel %}{% include kernel.markdown %}{% endcapture %}
{% capture compiler %}{% include compiler.markdown %}{% endcapture %}

<h2><a href="{{page.url}}">Docs »</a> <small>how to stuff</small></h2>
<ul class="nav nav-tabs">
  <li class="active"><a href="#kernel" data-toggle="tab">Writing x86 Kernel</a></li>
  <li class=""><a href="#compiler" data-toggle="tab">Writing Compiler</a></li>
</ul>

<div class="tab-content" id="my-tab-content">
  <div id="kernel" class="active tab-pane">
    {{ kernel | markdownify }}
  </div>
  <div id="compiler" class="tab-pane">
    {{ compiler | markdownify }}
  </div>
</div>

<script>
  $(function () {
    $('.tabs a:last').tab('show')
  })
</script>
