---
layout: page
title: Categories
permalink: /categories/
---

<nav align="center" class="menu archives text-center" aria-label="browse archives">
  <h2>Browse archives by:</h2>
  <a class="label_link" href="/archives">year</a>
  <a class="label_link active" href="/categories" aria-current="page">category</a>
  <a class="label_link" href="/tags">tag</a>
</nav><hr>

{% include archives/by-taxonomy.html taxonomy="Categories" items=site.categories %}