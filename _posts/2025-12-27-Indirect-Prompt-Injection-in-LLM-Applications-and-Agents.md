---
layout: single
title: "Indirect Prompt Injection in LLM Applications and Agents: Threat Models, Benchmarks, and Defense Mechanisms"
date: 2026-01-07 22:00:00 +0000
comments: true
tags: [Information Technology, GeoAI]
---

Indirect prompt injection (IPI) refers to attacks where malicious instructions are embedded in untrusted external content (e.g., web pages, documents, tool responses) and are later consumed by an LLM application alongside the user's intended instruction. Early work demonstrated that this threat is not hypothetical: real-world LLM-integrated systems can be manipulated through injected content, enabling outcomes such as data exfiltration and unintended tool misue.

Keywords:
*Indirect prompt injection, LLM agents, tool use, RAG security, runtime policy.*

# 1. Introduction and motivation

LLMs are increasingly deployed as integrated systems rather than standalone chatbots. This integration amplifies a core security tension: external content is essential for usefulness, yet it is not trustworthy by default. IPI captures the resulting failure mode when untrusted content is interpreted as actionable instruction rather than inert data.

In response, the research community has begun to standardize both evaluation and mitigation. On the evaluation side, BIPIA [[1]](#1) introduces a dedicated benchmark for IPI on LLM applications, while AgentDojo provides a dynamic environment for tool-using agents. On the mitigation side, the field has broadened from prompt-only countermeasures toward system and runtime approaches that constrain tool access and validate execution trajectories, reflecting an emerging consensus that IPI is often best addressed at the level of actions and control flow, not only text.

# 2. Problem definition and a minimal system model
## 2.1 What is Indirect Prompt Injection?

Indirect prompt injection (IPI) is the setting where adversarial instructions are embedded in untrusted external content (e.g., a web page or retrieved document) and then get included in the model's context, causing the system to follow those instructions instead of the user's intended goal.

A simple way to separate terms:
- Direct prompt injection: the attacker places instructions directly into the user's message.
- Indirect prompt injection: the attacker places instructions into third-party content that the system later consumes (retrieval, browsing, document ingestion, tool output).

We can describe most systems vulnerable to IPI using a simple diagram like the one below.
![flow_of_IPI_vulnerable_systems](/images/blogs/Indirect_Prompt_Injection_in_LLM_Applications_and_Agents/flow_of_IPI_vulnerable_systems.png)
*Figure 1. The flow of IPI vulnerable systems.*

## 2.2 What counts as "success" for an attacker
At a high level, IPI attempts to cause one (or more) of the following deviations:
- Instruction deviation: the model follows embedded instructions instead of the user's goal.
- Action deviation: the agent performs tool actions that are unjustified by the user task (common in agent settings).
- Persistence: injected content contaminates memory or long-lived state, affecting future steps.

# 3. Threat model and taxonomy
# 3.1 Threat model

Most IPI papers study a setting with three simple assumptions:
- The system mixes trusted instructions with untrusted content (retrieval, browsing, tool outputs) in a single context for the LLM.
- The attacker can influence some untrusted content that the system will later ingest (e.g., a web page that gets retrieved, a document in a corpus, or a tool output channel).
- The defender controls the orchestration (prompt template, tool routing, filtering, validation policies), but not the attacker-controlled external content.

## 3.2 Attack surface: where injected instructions can enter
There are several common entry points:
- Retrieval results (RAG docs)
- Browsing / web content
- Emails / files / documents that get ingested
- Tool outputs
- Agent memory / long-lived notes

That matches why agent benchmarks (e.g., AgentDojo [[2]](#2)) are useful: they exercise exactly these "untrusted inputs + tool execution" loops in a controlled way.

| Targets \ Entry points | Output content | Tool actions | Persistence |
|---|---|---|---|
| **Retrieval** | Retrieved text overrides user intent | Retrieved content steers tool calls | Injected doc seeds agent memory |
| **Tool outputs** | Tool return biases final answer | Tool result triggers unsafe follow-ups | Tool output stored for reuse |
| **Memory/state** | Contaminated memory distorts responses | Memory cues justify extra actions | Self-reinforcing long-term contamination |

# 4. Benchmarks
The IPI literature is increasingly benchmark-driven: papers are typically compared by how well they reduce attack success while preserving normal task performance, often in standardized testbeds.

## 4.1 Two common benchmark families
### (A) LLM-application benchmarks (content-mixing / RAG-style)
- An LLM app that ingests untrusted external text together with the user's goal, where the injected content tries to redirect behavior.
- It captures the classic IPI setting: instruction vs data confusion at the prompt/content level.
- Representative benchmark: **BIPIA** [[3]](#3) (widely cited in the defense literature for app-level IPI evaluation).

### (B) Tool-using agent benchmarks (action-level security)
- What it tests: end-to-end agents that plan, call tools, observe tool outputs, and iterate under attack.
- It evaluates what many practical systems care about: unsafe tool calls and harmful trajectories, not only incorrect text answers.
- Representative benchmark: **AgentDojo** [[2]](#2) (97 realistic tasks + 629 security test cases).

![Two benchmark families](/images/blogs/Indirect_Prompt_Injection_in_LLM_Applications_and_Agents/Two_benchmark_families.png)
*Figure 2. Two common benchmark families.*

## Common metrics
Most benchmark results can be interpreted with two numbers:
- Security: Attack Success Rate (ASR) / security failure rate -> Under attack, how often does the system still get compromised?
- Utility: Clean task success (no attack) -> How much normal performance is preserved?

# 5. Defense landscape

IPI defenses can be understood by where they intervene in the minimal system model: at the text level (what the LLM reads), at the decision level (what the LLM proposes), or at the execution level (what the system actually does). The recent trend, especially for tool agents, is a shift from "promp-only" mitigations toward system and execution constraints.

## 5.1 Defense families

![defense_stack_map_of_IPI](/images/blogs/Indirect_Prompt_Injection_in_LLM_Applications_and_Agents/defense_stack_map_forIPI.png)
*Figure 3* summarizes the defense landscape by where a method intervenes in an LLM app/agent pipeline. Each defense family in this section maps to one (or more) intervention points in the stack: (i) shaping untrusted text before it enters the prompt, (ii) encoding provenance/boundaries during prompt assembly, (iii) constraining tool execution, and (iv) validating multi-step trajectories during runtime.

### 1. Prompt-level provenance and boundary cues
- *Idea*: Make untrusted text look and behave like "data", not "instructions" by marking, restructuring, or spotlighting it in the prompt.
- *Representative*: **Spotlighting** [[4]](#4) (provenance highlighting / transformation)

### 2. Detection & sanitization
- *Idea*: detect injected instruction-like segments and/or sanitize external content before it enters the prompt.
- *Representative*: **InstructDetector** [[5]](#5) (instruction-state change detection), **DataFilter** [[6]](#6) (sanitization-focused).
![the_overview_of_data_filter](/images/blogs/Indirect_Prompt_Injection_in_LLM_Applications_and_Agents/the_overview_of_data_filter.png)
*Figure 4. The overview of DataFilter.*

### 3. Execution-centric validation
- *Idea*: validate plans/steps/tool calls against the user's goal or via re-execution checks; treat attacks as "trajectory deviations".
- *Representatives*: **Task Shield** [[7]](#7) (task-aligned action enforcement), **MELON** [[8]](#8) (masked re-execution with tool-comparison).
![The_flow_of_task_shield](/images/blogs/Indirect_Prompt_Injection_in_LLM_Applications_and_Agents/the_flow_of_task_shield.png)
*Figure 5. Overview of Task Shield.*

### 4. Structured constraints over agent behavior (graphs / programs)
- *Idea*: represent expected tool dependencies or execution traces as structured objects (graphs/programs), then enforce or verify allowed behavior.
- *Representatives*: IPIGuard [[9]](#9) (Tool Dependency Graph; constrained execution), AgentArmor [[10]](#10) (trace → CFG/DFG/PDG; program-analysis checks).

### 5. Planner-executor separation + memory isolation
- *Idea*: separate planning from acting; isolate memory streams; add validators to detect drift induced by untrusted content.
- Representative: DRIFT (Dynamic Rule-based Defense with Injection Isolation).
![the_flow_of_DRIFT](/images/blogs/Indirect_Prompt_Injection_in_LLM_Applications_and_Agents/the_workflow_of_DRIFT.png)

*Figure 6. The workflow of DRIFT.*

# 6. Current status and key takeaways

If you only remember a few things about IPI defenses, remember these.

## 6.1. The field's direction
- From prompt-only -> system/runtime controls: Early work emphasized prompt-level boundary cues; recent agent-focused defenses increasingly constrain actions and trajectories (tool gating, validation, structured execution).
- Agents change the evaluation target: For tool-using agents, security is not just about incorrect text but also unsafe tool calls and multi-step drift, which is why AgentDojo evaluation became central.
- Security is always coupled with utility: Defenses that reduce ASR but heavily reduce clean task success are rarely acceptable in practice; modern papers increasingly report both axes.
- Structured representations are rising: Several methods represent allowable behavior explicitly (tool dependency graphs, trace-as-program constraints) to make enforcement and verification more concrete than "hope the model obeys".

The landscape becomes easiest to remember when we map defenses by (i) whether the system is a single-step app or a multi-step tool agent, and (ii) whether the defense acts on text or on execution.
![which_ipi_defenses_fit_which_systems](/images/blogs/Indirect_Prompt_Injection_in_LLM_Applications_and_Agents/which_ipi_defenses_fit_which_systems.png)
*Figure 7. A 2x2 map of IPI defenses by system type and control layer.*

## 6.2. The recurring tradeoffs
- False positives vs security: detectors/sanitizers can over-block benign content, directly hurting utility.
- Coverage vs complexity: stronger agent controls require more orchestration (tool APIs, logs, validators,runtime hooks).
- Benchmark transfer: good performance on a benchmark does not automatically guarantee robustness across different agent frameworks, tools, or content distributions.

# 7. Open problems and where to go next
Even with strong recent progress, IPI is not “solved.” The most useful way to frame the current status is as a set of open evaluation and engineering questions:

- Results can vary significantly across agent frameworks, tool interfaces, and model families; benchmark wins may not transfer cleanly to new stacks.
- Many practical agents keep notes, plans, or summaries; once contaminated, injections can persist beyond a single step. Memory isolation is increasingly proposed, but evaluation of long-horizon persistence remains limited.
- Detection/sanitization can reduce attacks but may block benign content, leading to refusal or degraded task performance.
- For tool-using agents, the real question is not only "did the answer change", but "did the agent take unjustified actions across a trajectory?". This motivates execution-centric defenses, but also makes evaluation more complex.

IPI is best understood as a systems problem: untrusted content enters the reasoning loop and competes with intended instructions. Benchmarks like BIPIA and AgentDojo have helped standardize evaluation, and the clearest trend is toward defenses that constrain execution—not only prompts.

***Notice on figures and AI assistance.***

*Some figures in this post are reproduced or adapted from the original papers cited throughout the text; the source paper is indicated in the corresponding figure caption. Additional diagrams were generated using Nano Banana (Google) based on author-provided specifications. Parts of the writing (e.g., summarization, phrasing, and organization) were AI-assisted; readers should rely on the cited papers for definitive technical details and results.*

# References
<a id="1">[1]</a>
Jingwei Yi, Yueqi Xie, Bin Zhu, Emre Kiciman, Guangzhong Sun, Xing Xie, and Fangzhao Wu. Benchmarking and defending against indirect prompt injection attacks on large language models. In Proceedings of the 31st ACM SIGKDD Conference on Knowledge Discovery and Data Mining V.1, KDD ’25, pp. 1809–1820. ACM, July 2025. doi: 10.1145/3690624.3709179. URL http://dx.doi.org/10.1145/3690624.3709179.

<a id="2">[2]</a>
Edoardo Debenedetti, Jie Zhang, Mislav Balunovi´c, Luca Beurer-Kellner, Marc Fischer, and Florian Tram`er. Agentdojo: A dynamic environment to evaluate prompt injection attacks and defenses for llm agents, 2024. URL https://arxiv.org/abs/2406.13352.

<a id="3">[3]</a>
Jingwei Yi, Yueqi Xie, Bin Zhu, Emre Kiciman, Guangzhong Sun, Xing Xie, and Fangzhao Wu. Benchmarking and defending against indirect prompt injection attacks on large language models. In Proceedings of the 31st ACM SIGKDD Conference on Knowledge Discovery and Data Mining V.1, KDD ’25, pp. 1809–1820. ACM, July 2025. doi: 10.1145/3690624.3709179. URL http://dx.doi.org/10.1145/3690624.3709179.

<a id="4">[4]</a>
Keegan Hines, Gary Lopez, Matthew Hall, Federico Zarfati, Yonatan Zunger, and Emre Kiciman. Defending against indirect prompt injection attacks with spotlighting, 2024. URL https://arxiv.org/abs/2403.14720.

<a id="5">[5]</a>
Tongyu Wen, Chenglong Wang, Xiyuan Yang, Haoyu Tang, Yueqi Xie, Lingjuan Lyu, Zhicheng Dou, and Fangzhao Wu. Defending against indirect prompt injection by instruction detection, 2025. URL https://arxiv.org/abs/2505.06311.

<a id="6">[6]</a>
Yudong Wang, Zixuan Fu, Jie Cai, Peijun Tang, Hongya Lyu, Yewei Fang, Zhi Zheng, Jie Zhou, Guoyang Zeng, Chaojun Xiao, Xu Han, and Zhiyuan Liu. Ultra-fineweb: Efficient data filtering and verification for high-quality llm training data, 2025b. URL https://arxiv.org/abs/2505.05427

<a id="7">[7]</a>>
Feiran Jia, Tong Wu, Xin Qin, and Anna Squicciarini. The Task Shield: Enforcing Task Alignment to Defend Against Indirect Prompt Injection in LLM Agents, 2024. URL https://arxiv.org/abs/2412.16682.

<a id="8">[8]</a>
Kaijie Zhu, Xianjun Yang, Jindong Wang, Wenbo Guo, and William Yang Wang. Melon: Provable defense against indirect prompt injection attacks in ai agents, 2025. URL https://arxiv.org/abs/2502.05174.

<a id="9">[9]</a>
Hengyu An, Jinghuai Zhang, Tianyu Du, Chunyi Zhou, Qingming Li, Tao Lin, and Shouling Ji. Ipiguard: A novel tool dependency graph-based defense against indirect prompt injection in llm agents, 2025. URL https://arxiv.org/abs/2508.15310.

<a id="10">[10]</a>
Peiran Wang, Yang Liu, Yunfei Lu, Yifeng Cai, Hongbo Chen, Qingyou Yang, Jie Zhang, Jue Hong, and Ye Wu. Agentarmor: Enforcing program analysis on agent runtime trace to defend against prompt injection, 2025a. URL https://arxiv.org/abs/2508.01249.

***Author: Felix Do***