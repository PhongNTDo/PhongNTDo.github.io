---
permalink: /
title: "Phong (Felix) Do"
author_profile: true
redirect_from: 
  - /about/
  - /about.html
---


<!-- Welcome! I am an ***AI Researcher at Zalo AI & UIT@NLP*** at Ho Chi Minh city, Vietnam. I have been a researcher at [UIT@NLP lab](https://nlp.uit.edu.vn/home) during my undergraduate degree at [University of Information Technology](https://www.uit.edu.vn/). After receiving my bachelor's degree in computer science, I have continued to do part-time research at this lab until now. In parallel, I have worked as an AI researcher at [Zalo AI](https://zalo.ai/), Vietnam for 3+ years. -->


Hello and welcome! I’m Phong Do — a first-year PhD student at the [University of Warwick](https://warwick.ac.uk/), UK, supervised by [Dr. Gabriele Pergola](https://warwick.ac.uk/fac/sci/dcs/people/u1898418/). My research focuses on prompt stability, prompt robustness, and security risks in LLM-based systems, with particular emphasis on indirect prompt injection (IPI) in retrieval-augmented and agent-based settings

My journey into AI began at the [NLP@UIT](https://nlp.uit.edu.vn/home) lab at [University of Information Technology](https://www.uit.edu.vn/) in Ho Chi Minh City, where I specialized in question answering and machine reading comprehension as an undergraduate researcher. This early work laid the foundation for my long-term interest in building systems that can read, understand, and reason over text. After graduation, I continued my involvement with the lab and expanded my experience at [Zalo AI](https://zalo.ai/), where I spent over three years working on large language models and speech processing. At Zalo, I contributed to speech technologies for [Kiki Assistant](https://kiki.zalo.ai/), a Vietnamese virtual voice assistant installed in over one million cars, helping bring conversational AI into everyday life.

Beyond traditional NLP, I am also pursuing research in GeoAI — the intersection of geography and artificial intelligence. My interest lies in how spatial knowledge and geographic context can be modeled to improve information retrieval and reasoning, particularly in combination with RAG and Knowledge Graphs. By integrating linguistic and spatial perspectives, I aim to develop AI systems that not only understand language but also situate that understanding within the broader context of knowledge, culture, and place.


Research Interests
======

My research lies at the intersection of **large language models**, **retrieval**, and **AI safety**, with a focus on robustness, security, and structured reasoning.

- **Prompt Stability & Prompt Robustness**  
  Sensitivity analysis of prompts, instruction perturbations, evaluation of reliable instruction-following.

- **Indirect Prompt Injection (IPI) Defense**  
  Security of LLM agents, untrusted content handling, retrieval-time and execution-time defenses.

- **Retrieval-Augmented Generation (RAG) & Question Answering**  
  Document retrieval, evidence grounding, reasoning over retrieved context.

- **GeoAI & Spatial Reasoning**  
  Geographic knowledge graphs, spatial QA, geospatial retrieval and reasoning.


<span style="color:red">*I am happy to chat and discuss potential collaborations. Please feel free to reach out to me via Email (phong.do@warwick.ac.uk).*</span>


## 📝 Latest Blogs
------

Here are a few recent posts you might enjoy.

<div class="about-blog-grid">
  {% for post in site.posts limit:4 %}
    <article class="about-blog-card">
      <h3 class="about-blog-title"><a href="{{ post.url | relative_url }}">{{ post.title }}</a></h3>
      <p class="about-blog-excerpt">{{ post.excerpt | markdownify | strip_html | strip_newlines | truncatewords: 28 }}</p>
      <a class="about-blog-link" href="{{ post.url | relative_url }}">Read the full post</a>
    </article>
  {% endfor %}
</div>


## 📰 News
------

<details>
  <summary><strong>🎉 June 25, 2025 — I will be starting my PhD at the University of Warwick!</strong></summary>
  <p>I am thrilled to share that I have been awarded a fully-funded scholarship for the PhD in Computer Science program at the University of Warwick, UK. I will be starting this exciting journey in Fall 2025.</p>
</details>

<details>
  <summary><strong>📄 May 16, 2025 — A paper accepted to ACL 2025 (Main)</strong></summary>
  <p>Our new paper at Zalo with the title <em>"VMLU Benchmarks: A comprehensive benchmark toolkit for Vietnamese LLMs"</em> has been accepted at <strong>ACL 2025</strong>.</p>
</details>

<details>
  <summary><strong>📄 June 19, 2024 — My paper published at NAACL 2024 (Findings)</strong></summary>
  <p>Our new paper at the UIT NLP Group with the title <em>"VLUE: A New Benchmark and Multi-task Knowledge Transfer Learning for Vietnamese Natural Language Understanding"</em> has been published at <strong>NAACL 2024</strong>.</p>
</details>
