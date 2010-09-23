---
layout: default
title: 'Home'
---

{% for post in site.posts limit:2 %}
## <a href='{{post.url}}'>{{post.title}}</a>
<div class="entry-content">
    {{ post.content }}
</div>


<a href="http://twitter.com/share" class="twitter-share-button" data-text="{{post.title}}" data-url="http://serialized.net{{post.url}}" data-count="horizontal" data-via="jbarratt">Tweet</a>
<script type="text/javascript" src="http://platform.twitter.com/widgets.js">
</script>

{% endfor %}


<h2>Journal Archives</h2>
<ul class="posts">
{% for post in site.posts limit:8 %}
<li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ post.url }}">{{ post.title }}</a></li>
{% endfor %}
<li><a href="/archives.html">Complete archive</a></li>
</ul>
