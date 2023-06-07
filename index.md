---
layout: page
title: About Me
subtitle: I'm Mehran Zand a programmer and founder
sitemap:
priority: 0.9
---
<div id="describe-text">
	<p>I'm Mehran Zand a programmer and founder who emphasizes on the business value and quality together. Having years of experience of technical stuff in the field of software development, as a developer, architect and project manager in various companies as well as a deep entrepreneurial vision has enabled me to better help companies.</p>
</div>

<br/>
> Latest Blog Posts
{% for post in site.posts limit:4 %}
{% assign wordCount = post.content | number_of_words %}
<a href="{{ post.url | prepend: site.baseurl }}">
	<span class="post-teaser__title">{{ post.title }} {% if forloop.first %}<strong class="blink">New</strong>{% endif %}</span>
	<span class="post-teaser__date">{{ post.date | date: "%d %B %Y" }}</span>
	<span class="post-teaser__words">{{ wordCount }} words</span>
</a>
{% endfor %}

