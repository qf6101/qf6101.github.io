---
layout: page
title: Blog of Feng Qian
tagline: Supporting tagline
---
{% include JB/setup %}

<ul class="posts">

  {% for post in site.posts %}
    <div class="post">
    <h3 class="title"><a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></h3>
    <p class="meta">Date: {{ post.date }}</p>
    <div class="entry">
      {{ post.content | strip_html | truncatewords: 10 }}
            <a href="{{ BASE_PATH }}{{ post.url }}">阅读全文</a>
    </div>
  </div>
    <br/>
 {% endfor %}

</ul>


