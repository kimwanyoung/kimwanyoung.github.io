---
layout: home
title: 완영 블로그
permalink: /
---

# 안녕하세요, 완영입니다 👋
---

## 📚 카테고리별 글 모음

### Spring
{% assign spring_posts = site.posts | where: "category", "Spring" %}
{% for post in spring_posts limit:5 %}
- [{{ post.title }}]({{ post.url }}) - {{ post.date | date: "%Y.%m.%d" }}
{% endfor %}
{% if spring_posts.size == 0 %}- 아직 작성된 글이 없습니다.{% endif %}

### SQL
{% assign sql_posts = site.posts | where: "category", "SQL" %}
{% for post in sql_posts limit:5 %}
- [{{ post.title }}]({{ post.url }}) - {{ post.date | date: "%Y.%m.%d" }}
{% endfor %}
{% if sql_posts.size == 0 %}- 아직 작성된 글이 없습니다.{% endif %}

### CS
{% assign cs_posts = site.posts | where: "category", "CS" %}
{% for post in cs_posts limit:5 %}
- [{{ post.title }}]({{ post.url }}) - {{ post.date | date: "%Y.%m.%d" }}
{% endfor %}
{% if cs_posts.size == 0 %}- 아직 작성된 글이 없습니다.{% endif %}

### 네트워크
{% assign network_posts = site.posts | where: "category", "Network" %}
{% for post in network_posts limit:5 %}
- [{{ post.title }}]({{ post.url }}) - {{ post.date | date: "%Y.%m.%d" }}
{% endfor %}
{% if network_posts.size == 0 %}- 아직 작성된 글이 없습니다.{% endif %}

---

## 📝 최근 포스트

{% for post in site.posts limit:5 %}
- **[{{ post.title }}]({{ post.url }})**
  {{ post.excerpt | strip_html | truncatewords: 20 }}
  *{{ post.date | date: "%Y년 %m월 %d일" }}* · {{ post.category }}
{% endfor %}

---

## 📬 Contact & Social Links

- **Email**: [dhks2869@gmail.com](mailto:dhks2869@gmail.com)
- **GitHub**: [@kimwanyoung](https://github.com/kimwanyoung)

---

<div style="text-align: center; color: #666; font-size: 0.9em; margin-top: 2em;">
  © 2025 kimwanyoung. All rights reserved.
</div>
