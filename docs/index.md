---
layout: default
title: Home
---

<div class="breadcrumb">
  <span>All Posts</span>
  <span>›</span>
  <span>Home</span>
</div>

<h1 class="page-title">Bienvenue sur mon blog IA &amp; portfolio</h1>
<p class="page-date">Mis à jour le {{ site.time | date: "%d %B %Y" }}</p>

<div class="divider"></div>

Je suis étudiant en **Data Science & Cloud Computing**, passionné par l'intelligence artificielle, les LLMs, et le Data Engineering. Ce site est l'endroit où je publie mes notes, articles, et projets.

Vous trouverez ici des articles techniques sur les grands modèles de langage, le déploiement de modèles ML en production, les systèmes RAG, l'ingénierie des agents, et bien plus.

> "Je n'écris pas pour montrer ce que je sais — j'écris pour comprendre ce que j'apprends."

## Derniers articles

<div class="post-list">
{% for post in site.posts %}
<a href="{{ post.url | relative_url }}" class="post-item">
  <div class="post-item-meta">
    {% for cat in post.categories limit: 1 %}
    <span class="post-tag">{{ cat }}</span>
    {% endfor %}
    <span class="post-date-sm">{{ post.date | date: "%d %b %Y" }}</span>
  </div>
  <div class="post-item-title">{{ post.title }}</div>
  <div class="post-item-desc">{{ post.excerpt | strip_html | truncate: 140 }}</div>
</a>
{% endfor %}
</div>
