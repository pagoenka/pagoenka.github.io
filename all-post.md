---
layout: page
title: All Posts
permalink: /all-post/
---

<ul class="all-post-list">
    {% for post in site.posts %}
      <li>
      	<span class="post-meta">{{ post.date | date: "%b %-d, %Y" }}</span>
        <h5>
          <a class="post-link" href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a>
        </h5>
      </li>
    {% endfor %}
  </ul>