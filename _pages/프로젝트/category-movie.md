---
title: "영화 웹 사이트"
layout: archive
permalink: categories/Movie
author_profile: true
sidebar_main: true
---

{% assign posts = site.categories.Movie %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}
