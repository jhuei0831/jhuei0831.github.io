---
title: Search
layout: page
permalink: "/search/"
---

<!-- Html Elements for Search -->
<div id="search-container" class="form__input half">
	<input type="text" id="search-input" placeholder="search...">	
</div>
<ul id="results-container"></ul>

<!-- Script pointing to search-script.js -->
<script src="/assets/js/search-script.js" type="text/javascript"></script>

<!-- Configuration -->
<script>
SimpleJekyllSearch({
  searchInput: document.getElementById('search-input'),
  resultsContainer: document.getElementById('results-container'),
  json: '/search.json'
})
</script>
