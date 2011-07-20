---
layout: default
title: 'Home'
---

{% for post in site.posts limit:2 %}
## <a href='{{post.url}}'>{{post.title}}</a>
<div class="entry-content">
    {{ post.content }}
</div>

<div class="share">
  <div class="share_button">
      <div id="fb-root"></div>
      <script src="http://connect.facebook.net/en_US/all.js#xfbml=1"></script>
      <fb:like href="http://serialized.net{{post.url}}" send="false" layout="button_count" width="450" show_faces="true" font=""></fb:like>
  </div>
  <div class="share_button">
      <g:plusone size="small" href="http://serialized.net{{post.url}}"></g:plusone>
  </div>
  <div class="share_button">
      <a href="http://twitter.com/share" class="twitter-share-button" data-text="{{post.title}}" data-url="http://serialized.net{{post.url}}" data-count="horizontal" data-via="jbarratt">Tweet</a>
      <script type="text/javascript" src="http://platform.twitter.com/widgets.js">
      </script>
  </div>
</div>

{% endfor %}


<h2>Journal Archives</h2>
<ul class="posts">
{% for post in site.posts limit:8 %}
<li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ post.url }}">{{ post.title }}</a></li>
{% endfor %}
<li><a href="/archives.html">Complete archive</a></li>
</ul>
