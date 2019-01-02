---
layout: default
title: "Criptomonedas"
description: Criptomonedas y Productos Blockchain
permalink: /criptomonedas/
---

{% for post in site.categories.criptomonedas %}
<div class="post-preview">
    <a href="{{ post.url | prepend: site.baseurl }}">
        <h1 class="post-title-list">            {{ post.title }}
        </h1>
        {% if post.description %}
        <h3 class="post-description">
            {{ post.description }}
        </h3>
        {% endif %}
    </a>
    <p class="post-meta" style="margin-bottom:5px">
        Publicado el 
        {{ post.date | date: "%-d de " }}
        {% assign month = post.date | date: "%m" %}
        {% case month %}

          {% when '01' %} Enero
          {% when '02' %} Febrero
          {% when '03' %} Marzo
          {% when '04' %} Abril
          {% when '05' %} Mayo
          {% when '06' %} Junio
          {% when '07' %} Julio
          {% when '08' %} Agosto
          {% when '09' %} Septiembre
          {% when '10' %} Octubre
          {% when '11' %} Noviembre
          {% when '12' %} Diciembre

        {% endcase %}
       
        {{ post.date | date: "de %Y" }}
    </p>
	<div class="notepad-index-post-tags" style="">
		{% for tag in post.tags %}<a href="{{ site.baseurl }}/search/index.html#{{ tag | cgi_encode }}" title="Other posts from the {{ tag | capitalize }} tag">{{ tag | capitalize }}</a>{% unless forloop.last %}&nbsp;{% endunless %}{% endfor %}
	</div>
</div>
{% endfor %}

<!-- Pager -->
{% if paginator.total_pages > 1 %}
<ul class="pager">
    {% if paginator.previous_page %}
    <li class="previous">
        <a href="{{ paginator.previous_page_path | prepend: site.baseurl | replace: '//', '/' }}">&larr; Newer Posts</a>
    </li>
    {% endif %}
    {% if paginator.next_page %}
    <li class="next">
        <a href="{{ paginator.next_page_path | prepend: site.baseurl | replace: '//', '/' }}">Older Posts &rarr;</a>
    </li>
    {% endif %}
</ul>
{% endif %}
