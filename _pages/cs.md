---
title: CS
author: kimwanyoung
date: 2025-10-06
category: CS
layout: post
---

{% assign cs_posts = site.posts | where: "category", "CS" %}
{% if cs_posts.size > 0 %}
{% for post in cs_posts %}
- [{{ post.title }}]({{ post.url }}) - {{ post.date | date: "%Y.%m.%d" }}
{% endfor %}
{% else %}
아직 작성된 글이 없습니다.
{% endif %}
