---
layout: default
title: Home
---

<div class="breadcrumb">
  <span>All Posts</span>
  <span>›</span>
  <span>Home</span>
</div>

<h1 class="page-title">Bienvenue sur mon blog IA</h1>

<p class="page-date">
  Mis à jour le {{ site.time | date: "%d %B %Y" }}
</p>

<div class="divider"></div>

<img
  src="{{ '/assets/images/handson.jpg' | relative_url }}"
  alt="Hands-on AI"
  class="hero-image"
>

<p>
  Je suis étudiant en <strong>Génie informatique à ENSAO</strong>, passionné par
  l'intelligence artificielle, les LLMs, et le Backend Engineering. Ce site est
  l'endroit où je publie mes notes, articles, et projets.
</p>

<p>
  Vous trouverez ici des articles techniques sur les grands modèles de langage,
  le déploiement de modèles ML en production, les systèmes RAG,
  l'ingénierie des agents, et bien plus.
</p>

<blockquote>
  <p>
    "J'aime lire les articles, les versionner par moi-même, partager mon avis
    et leur efficacité pour la mise en œuvre dans des projets réels."
  </p>
</blockquote>

<h2>Derniers articles</h2>

<div class="post-list">

{% for post in site.posts %}

<a href="{{ post.url | relative_url }}" class="post-item">

  {% include cover.html item=post mode="thumb" %}

  <div class="post-item-body">

    <div class="post-item-meta">

      {% for cat in post.categories limit: 1 %}
        <span class="post-tag">{{ cat }}</span>
      {% endfor %}

      <span class="post-date-sm">
        {{ post.date | date: "%d %b %Y" }}
      </span>

    </div>

    <div class="post-item-title">
      {{ post.title }}
    </div>

    <div class="post-item-desc">
      {{ post.excerpt | strip_html | truncate: 140 }}
    </div>

  </div>

</a>

{% endfor %}

</div>