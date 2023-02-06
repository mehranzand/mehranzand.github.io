---
layout: page
title: Blog
subtitle: Posts by Category
---

<div>
{% assign postsCategory = site.posts | group_by_exp:"post", "post.categories"  %}
{% for category in postsCategory %}
<h4 class="post-teaser__month">
<strong>
{% if category.name %} 
- - - - -  {{ category.name }} - - - - - 
{% else %} 
{{ Print }} 
{% endif %}
</strong>
</h4>
<ul class="list-posts">
{% for post in category.items %}
{% assign wordCount = post.content | number_of_words %}
<li class="post-teaser">
	<a href="{{ post.url | prepend: site.baseurl }}">
		<span class="post-teaser__title">{{ post.title }}</span>
		<span class="post-teaser__date">{{ post.date | date: "%d %B %Y" }}</span>
		<span class="post-teaser__words">{{ wordCount }} words</span>
	</a>
</li>
{% endfor %}
</ul>
{% endfor %}
</div>
