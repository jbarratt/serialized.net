---
layout: default
title: 'Home'
---

{% assign post = site.posts.first %}
## <a href='{{post.url}}'>{{post.title}}</a>

<div id="entry-content">
{{ post.content }}
</div>

<h2>Journal Archives</h2>
<ul class="posts">
{% for post in site.posts limit:5 %}
<li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ post.url }}">{{ post.title }}</a></li>
{% endfor %}
<li><a href="/archives">Complete archive</a></li>
</ul>
