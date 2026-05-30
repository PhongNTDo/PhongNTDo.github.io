---
layout: single
title: Experience
permalink: /experience/
author_profile: true
---

{% include base_path %}

<p class="experience-summary">
  A chronological view of my work across doctoral research, applied AI engineering, and NLP research.
</p>

<div class="experience-timeline">
  {% for experience in site.data.experience %}
    <article class="experience-entry{% if experience.status == 'current' %} experience-entry--current{% endif %}">
      <div class="experience-period">{{ experience.date }}</div>
      <div class="experience-card">
        {% if experience.status == "current" %}
          <span class="experience-badge">Current</span>
        {% endif %}
        <h2 class="experience-title">{{ experience.title }}</h2>
        <p class="experience-company">{{ experience.company }}</p>
        {% if experience.supervisor %}
          <p class="experience-supervisor"><strong>Supervisor:</strong> {{ experience.supervisor }}</p>
        {% endif %}
        <div class="experience-description">
          {{ experience.description | markdownify }}
        </div>
      </div>
    </article>
  {% endfor %}
</div>
