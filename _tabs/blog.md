---
layout: default
title: Blog
icon: fas fa-blog
order: 1
permalink: /
sitemap:
  priority: 0.9
  changefreq: weekly
---

<!-- SEO Enhancement for Blog Page -->
<meta
  name="description"
  content="Cybersecurity blog by sOn4jit featuring CTF writeups, TryHackMe and Hack The Box labs, penetration testing notes, offensive security methodologies, and hands-on cybersecurity learning."
/>
<meta
  name="keywords"
  content="sOn4jit blog, son4jit cybersecurity, CTF writeups, TryHackMe writeups, Hack The Box labs, penetration testing notes, offensive security, red teaming, cybersecurity notes"
/>

<div class="mb-5">
  <h1 class="text-center mb-3">sOn4jit’s Blog</h1>
  <p class="text-center text-muted">
    Cybersecurity insights, TryHackMe & HackTheBox rooms writeups, Penetration Testing guides & tutorial
  </p>
</div>

<div id="post-list" class="flex-grow-1 px-xl-1">
  {% for post in site.posts %}
  <article class="card-wrapper card">
    <a
      href="{{ post.url | relative_url }}"
      class="post-preview row g-0 flex-md-row-reverse"
    >
      <div class="col-md-12">
        <div class="card-body d-flex flex-column">
          <h1 class="card-title my-2 mt-md-0">{{ post.title }}</h1>

          <div class="card-text content mt-0 mb-3">
            <p>
              {% if post.description and post.description != "Overview" %} {{
              post.description }} {% else %} {{ post.content | strip_html |
              truncatewords: 30 }} {% endif %}
            </p>
          </div>

          <div class="post-meta flex-grow-1 d-flex align-items-end">
            <div class="me-auto">
              <i class="far fa-calendar fa-fw me-1"></i>
              <time data-ts="{{ post.date | date: '%s' }}" data-df="ll">
                {{ post.date | date: "%b %d, %Y" }}
              </time>

              {% if post.categories.size > 0 %}
              <i class="far fa-folder-open fa-fw ms-1 me-1"></i>
              <span class="categories">
                {% for category in post.categories %} {{ category }} {%- unless
                forloop.last -%}, {%- endunless -%} {% endfor %}
              </span>
              {% endif %}
            </div>
          </div>
        </div>
      </div>
    </a>
  </article>
  {% endfor %}
</div>
