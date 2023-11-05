---
layout: default
title: Блог
permalink: /blog.html
---

<ul id="posts">
	{% for post in site.posts %}
		{% include post.html %}
	{% endfor %}
</ul>
