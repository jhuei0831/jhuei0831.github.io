---
title: Tags
layout: page
permalink: "/tags/"
---
<nav align="center" class="menu archives text-center" aria-label="browse archives">
  <h2>Browse archives by:</h2>
  <a href="/archives" class="label_link">year</a>
  <a href="/categories" class="label_link">category</a>
  <a href="/tags" class="label_link active" aria-current="page">tag</a>
</nav>

{% include archives/by-taxonomy.html taxonomy="Tags" items=site.tags %}
