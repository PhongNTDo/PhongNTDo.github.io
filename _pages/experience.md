---
layout: single
title: Experience
permalink: /experience/
author_profile: true
---

{% include base_path %}

<div id="experience-timeline">
  {% for experience in site.data.experience %}
    <div class="experience-item">
      <div class="experience-date">{{ experience.date }}</div>
      <div class="experience-content">
        <h3>{{ experience.title }}</h3>
        <h4>{{ experience.company }}</h4>
        {% if experience.supervisor %}
          <p>Supervisor: {{ experience.supervisor }}</p>
        {% endif %}
        <p>{{ experience.description }}</p>
      </div>
    </div>
  {% endfor %}
</div>

<style>
  #experience-timeline {
    position: relative;
    width: 100%;
    margin: 0 auto;
    padding: 20px 0;
    margin-left: 25%; /* Add a left margin to push the timeline away from the sidebar */
  }

  .experience-item {
    position: relative;
    margin-bottom: 30px;
    padding-left: 40px;
  }

  .experience-item:before {
    content: '';
    position: absolute;
    top: 0;
    left: 0;
    width: 10px;
    height: 100%;
    background-color: #ddd; /* Timeline line color */
    z-index: 1;
  }

  .experience-item:after {
    content: '';
    position: absolute;
    top: 0;
    left: -5px;
    width: 20px;
    height: 20px;
    border-radius: 50%;
    background-color: #fff; /* Circle color */
    border: 3px solid #007bff; /* Circle border color */
    z-index: 2;
  }

  .experience-date {
    position: absolute;
    top: 0;
    left: -120px;
    width: 100px;
    text-align: right;
    font-weight: bold;
  }

  .experience-content {
    background-color: #f8f8f8; /* Content box color */
    padding: 15px;
    border-radius: 5px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  }
  /* Media query for smaller screens */
  @media (max-width: 768px) {
    #experience-timeline {
      margin-left: 0; /* Remove the left margin on smaller screens */
    }
    .experience-date {
        left: -100px;
    }
  }
</style>
