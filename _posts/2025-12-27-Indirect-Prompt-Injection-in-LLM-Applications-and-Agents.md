---
layout: single
title: "Indirect Prompt Injection in LLM Applications and Agents: Threat Models, Benchmarks, and Defense Mechanisms"
date: 2025-12-27 22:00:00 +0000
comments: true
tags: [Information Technology, GeoAI]
---

Indirect prompt injection (IPI) refers to attacks where malicious instructions are embedded in untrusted external content (e.g., web pages, documents, tool responses) and are later consumed by an LLM application alongside the user's intended instruction. Early work demonstrated that this threat is not hypothetical: real-world LLM-integrated systems can be manipulated through injected content, enabling outcomes such as data exfiltration and unintended tool misue.

Keywords:
*Indirect prompt injection, LLM agents, tool use, RAG security, runtime policy.*

# 1. Introduction and motivation

LLMs are increasingly deployed as integrated systems rather than standalone chatbots. This integration amplifies a core security tension: external content is essential for usefulness, yet it is not trustworthy be default. IPI captures the resulting failure mode when untrusted content is interpreted as actionable instruction rather than inert data.

The risk was first made concrete by work showing that prompts embedded in retrieved web content can 

***Author: Felix Do***