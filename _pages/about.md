---
permalink: /
title: "Phong (Felix) Do"
author_profile: true
redirect_from: 
  - /about/
  - /about.html
---


<!-- Welcome! I am an ***AI Researcher at Zalo AI & UIT@NLP*** at Ho Chi Minh city, Vietnam. I have been a researcher at [UIT@NLP lab](https://nlp.uit.edu.vn/home) during my undergraduate degree at [University of Information Technology](https://www.uit.edu.vn/). After receiving my bachelor's degree in computer science, I have continued to do part-time research at this lab until now. In parallel, I have worked as an AI researcher at [Zalo AI](https://zalo.ai/), Vietnam for 3+ years. -->

Hi, I am Phong Do, a first-year PhD student in Computer Science at the [University of Warwick](https://warwick.ac.uk/), supervised by [Dr. Gabriele Pergola](https://warwick.ac.uk/fac/sci/dcs/people/u1898418/).

My research focuses on building more reliable, robust, and adaptive LLM-based systems. I am especially interested in how large language models reason, retrieve evidence, follow instructions, and execute workflows in complex settings such as retrieval-augmented generation and agent-based systems.

Recently, my work has been moving toward LLM workflow engineering: how to represent, analyze, optimize, and adapt the reasoning structure of LLM systems. Instead of treating an LLM system as only one prompt and one answer, I am interested in the full workflow: planning, retrieval, tool use, model calls, verification, aggregation, and final response generation.

Before starting my PhD, I worked as an AI Engineer at [Zalo AI](https://zalo.ai/), where I contributed to language and speech technologies for real-world Vietnamese AI applications. I also worked with the [UIT NLP Group](https://nlp.uit.edu.vn/home), where my research focused on Vietnamese NLP, machine reading comprehension, question answering, and language models.

My research interests
======

- LLM workflows and agentic systems
- Prompt robustness and prompt stability
- Retrieval-augmented generation
- Indirect prompt injection and LLM security
- Question answering and machine reading comprehension
- Multilingual NLP, especially Vietnamese NLP
- Knowledge graphs and structured reasoning


<span style="color:red">*I am happy to chat and discuss potential collaborations. Please feel free to reach out to me via Email (phong.do@warwick.ac.uk).*</span>


## 📝 Latest Blogs


<div class="about-blog-grid">
  {% for post in site.posts limit:4 %}
    <a class="about-blog-card" href="{{ post.url | relative_url }}" title="{{ post.title | escape }}">
      <span class="about-blog-title">{{ post.title }}</span>
    </a>
  {% endfor %}
</div>


## News

<div class="news-timeline">
  <article class="news-item">
    <time class="news-date" datetime="2025-10">October 2025</time>
    <div class="news-content">
      <h3>Started my PhD at the University of Warwick</h3>
      <p>I began my fully funded PhD in Computer Science at the University of Warwick, UK, supervised by <a href="https://warwick.ac.uk/fac/sci/dcs/people/u1898418/">Dr. Gabriele Pergola</a>.</p>
    </div>
  </article>

  <article class="news-item">
    <time class="news-date" datetime="2025-05-16">May 16, 2025</time>
    <div class="news-content">
      <h3>Paper accepted to ACL 2025 Main</h3>
      <p>Our paper at Zalo, <em>VMLU Benchmarks: A comprehensive benchmark toolkit for Vietnamese LLMs</em>, was accepted to <strong>ACL 2025</strong>.</p>
    </div>
  </article>

  <article class="news-item">
    <time class="news-date" datetime="2024-06-19">June 19, 2024</time>
    <div class="news-content">
      <h3>Paper published at NAACL 2024 Findings</h3>
      <p>Our paper with the UIT NLP Group, <em>VLUE: A New Benchmark and Multi-task Knowledge Transfer Learning for Vietnamese Natural Language Understanding</em>, was published at <strong>NAACL 2024 Findings</strong>.</p>
    </div>
  </article>
</div>
