---
layout: single
title: "From ReAct to AFlow: The Workflow Turn in LLM Agents"
date: 2026-05-21 22:00:00 +0000
comments: true
tags: [Information Technology, ]
---

# 1. LLM agents are no longer just prompts

## 1. From Prompts to Workflows

Large language models were once mostly used through a simple interface: write a prompt, get an answer. But modern LLM agents are no longer just prompt-response systems. They can plan, call tools, observe results, reflect on mistakes, communicate with other agents, and search for better ways to solve a task.

This shift changes the main question.

Instead of asking:

> **What prompt should we write?**

we are increasingly asking:

> **What workflow should the system execute?**

<!-- 
Figure 1 suggestion:
Create a custom diagram titled "From Prompt Engineering to Workflow Engineering".

The figure should show a simple vertical or horizontal flow:

Prompt
→ Reasoning / Planning
→ Tool or Model Calls
→ Observation / Communication
→ Aggregation
→ Final Answer

Add small side labels:
- ReAct: reasoning + acting
- ReWOO: plan then execute
- Reflexion: feedback loop
- DSPy: programmable pipeline
- MoA / GPTSwarm: multi-agent collaboration
- ADAS / AFlow / ScoreFlow / A2Flow: workflow search
- MaAS / Difficulty-Aware Orchestration: adaptive routing
-->

![From Prompt Engineering to Workflow Engineering](/images/blogs/the_workflow_turn_in_llm_agents/from_prompt_to_workflow.png)

*Figure 1. A simplified view of the shift from prompt engineering to workflow engineering. Instead of treating an LLM system as one prompt and one answer, recent agentic systems organize reasoning, tools, models, communication, and feedback into structured workflows.*

A workflow can be a reasoning-action loop, as in [ReAct](https://arxiv.org/abs/2210.03629); a plan-then-execute structure, as in [ReWOO](https://arxiv.org/abs/2305.18323); or a self-improving loop, as in [Reflexion](https://arxiv.org/abs/2303.11366). It can also be a programmable pipeline, as in [DSPy](https://arxiv.org/abs/2310.03714), or a multi-agent communication graph, as in GPTSwarm.

More recent works push this idea further. ADAS, AFlow, ScoreFlow, and A2Flow ask whether agentic workflows can be automatically designed or optimized. MaAS and Difficulty-Aware Agent Orchestration ask whether different queries should use different workflows, models, or computational budgets.

So this blog is not a paper-by-paper summary. Instead, it is a map of a bigger transition:

> **LLM agents are moving from prompt engineering to workflow engineering.**

We will organize the papers around one central question:

> **Given a user query, how should an AI system decide the right sequence of reasoning, tools, models, agents, and computation to use?**

This view helps us compare the papers more clearly. Some papers define the basic control loop. Some introduce programming abstractions. Some explore multi-agent communication. Some search for better workflows. Others adapt the workflow based on query difficulty or cost.

In short, the field is moving from **better prompts** to **better systems**.

# 2. A minimal model of an agentic workflow

Before comparing the papers, it is useful to define what we mean by a **workflow**.

In this blog, I use *workflow* to mean the structure that controls how an LLM system moves from a user query to a final answer. This structure may include reasoning steps, tool calls, model calls, communication between agents, feedback, memory, routing, and evaluation.

A simple workflow can be written as:

```text
Query → Decide what to do → Execute steps → Collect information → Combine results → Answer
````

This view is intentionally simple. Different papers make different parts of this process more explicit.

For example, [ReAct](https://arxiv.org/abs/2210.03629) focuses on the loop between reasoning, action, and observation. [ReWOO](https://arxiv.org/abs/2305.18323) separates planning from tool execution. [Reflexion](https://arxiv.org/abs/2303.11366) adds feedback after failure. [DSPy](https://arxiv.org/abs/2310.03714) treats the workflow as a programmable pipeline. Multi-agent methods such as Mixture-of-Agents and GPTSwarm focus on how multiple models or agents communicate and aggregate their outputs.

So, instead of asking whether one paper is simply “better” than another, we can ask a more useful question:

> **Which part of the workflow does this method redesign?**

This question will guide the rest of the blog.

# 3. Reasoning-action loops: ReAct, ReWOO, Reflexion

## 3. Reasoning-Action Loops: How an Agent Controls Its Next Step

The first wave of LLM agent papers focused on a basic question: **how should one agent move from thought to action?**

A simple LLM answers directly. An agent, however, often needs to reason, call tools, observe results, and revise its next step. This is where early agentic workflows such as [ReAct](https://arxiv.org/abs/2210.03629), [ReWOO](https://arxiv.org/abs/2305.18323), and [Reflexion](https://arxiv.org/abs/2303.11366) become important. They do not mainly search for a new workflow automatically. Instead, they define different **control patterns** for how an agent behaves.

![Three Basic Agent Control Patterns](/images/blogs/the_workflow_turn_in_llm_agents/three_basic_agent_control_patterns.png)

*Figure 2. Three early control patterns for LLM agents: ReAct interleaves reasoning and acting, ReWOO separates planning from execution, and Reflexion adds a feedback loop for self-improvement.*

**ReAct** is the most intuitive starting point. It lets the model alternate between reasoning and acting: the model thinks about what to do, performs an action such as calling a tool or searching for information, observes the result, and then continues reasoning. This makes the agent more interactive than a single prompt-response system.

**ReWOO** changes the structure. Instead of repeatedly mixing reasoning and tool observations, it first creates a plan, then executes the required tool calls, and finally uses the collected evidence to produce the answer. This makes the workflow more separated: planning happens before execution.

**Reflexion** adds another idea: after an attempt, the agent can reflect on what went wrong and store this feedback as memory for future trials. In this view, the workflow does not end at the first answer. It can include a loop of trying, receiving feedback, reflecting, and trying again.

These three papers show that even before workflow search or multi-agent systems, there were already several choices about **control flow**:

- Should the agent act step by step?
- Should it plan everything first?
- Should it learn from feedback across attempts?

This is the first important layer of the workflow landscape. Before we ask how to automatically design workflows, we need to understand the basic patterns that workflows are built from.

# 4. Programmable workflows: DSPy

The previous section focused on **control patterns**: how an agent reasons, acts, observes, or reflects. But as workflows become more complex, another problem appears: manually writing prompts for every step is fragile and hard to optimize.

[DSPy](https://arxiv.org/abs/2310.03714) represents an important shift in this direction. Instead of treating prompting as an informal craft, DSPy treats LM systems as **programs** made of modules, signatures, metrics, and optimizers. In other words, the developer does not only write a prompt. The developer defines what each module should take as input, what it should produce, and what metric should be optimized.

![From Prompt Chains to Programmable Workflows](/images/blogs/the_workflow_turn_in_llm_agents/dspy-programmable-workflows.png)

*Figure 3. DSPy reframes LM systems as programmable workflows. Instead of manually tuning prompts at every step, developers define modules, signatures, and metrics, then use optimization to improve the pipeline.*

This idea matters because it changes the unit of design. In a normal prompt chain, each step is often a piece of text written by hand. In a DSPy-style workflow, each step becomes a module with a clearer interface. This makes the workflow easier to inspect, reuse, and optimize.

For the landscape of agentic systems, DSPy gives us an important vocabulary. It suggests that workflows should not only be described in natural language. They can also be represented as structured programs. Once a workflow is programmable, it becomes easier to ask new questions:

- Can we optimize the instructions inside each module?
- Can we evaluate the whole pipeline with a task metric?
- Can we compare different workflow structures more systematically?

This is why DSPy is a bridge between early agent loops and later workflow-search methods. ReAct, ReWOO, and Reflexion show different ways an agent can behave. DSPy shows how such behavior can be wrapped into a more systematic programming and optimization framework.

In short, DSPy moves the discussion from:

> **How do we write better prompts?**

to:

> **How do we program and optimize LM workflows?**

# 5. Multi-agent collaboration: Mixture-of-Agents and GPTSwarm

So far, the workflow has mostly looked like a single agent moving through reasoning, tools, and feedback. But another natural question is:

> **What if one model or one agent is not enough?**

This is where multi-agent collaboration becomes important. Instead of asking one LLM to solve the task alone, we can ask several models or agents to generate, critique, refine, or aggregate information. The workflow is no longer only about the steps inside one agent. It is also about **how agents communicate**.

[Mixture-of-Agents](https://arxiv.org/abs/2406.04692) is a simple but powerful example. It organizes multiple LLMs into layers. Agents in one layer generate candidate responses, and agents in the next layer use these responses as context to produce stronger outputs. The key idea is that models can benefit from the answers of other models, even when they are not fine-tuned together.

[GPTSwarm](https://proceedings.mlr.press/v235/zhuge24a.html) takes a more structural view. Instead of a fixed layered pipeline, it represents agents and their communication as a graph. Agents are nodes, and communication paths are edges. This makes the design question more explicit: **which agents should talk to which other agents, and in what structure?**

![Two Views of Multi-Agent Collaboration](/images/blogs/the_workflow_turn_in_llm_agents/multi-agent-collaboration.png)

*Figure 4. Mixture-of-Agents and GPTSwarm represent two views of multi-agent collaboration. MoA emphasizes layered aggregation across multiple models, while GPTSwarm emphasizes graph-structured communication among agents.*

The difference is subtle but important. Mixture-of-Agents asks how multiple model outputs can be combined to improve the final answer. GPTSwarm asks how the communication structure among agents should be organized. One is closer to **layered ensembling**; the other is closer to **workflow topology**.

This gives us a new layer in the workflow landscape. Once multiple agents are involved, the system designer must decide not only what each agent does, but also:

- how many agents to use,
- what role each agent should play,
- which agents should communicate,
- how their outputs should be aggregated,
- and whether the communication structure should be fixed or optimized.

In short, multi-agent systems turn workflow engineering into a problem of **collaboration design**. The unit of design is no longer just a prompt, a module, or a tool call. It becomes the communication pattern among several agents.

# 6. Automated Workflow Search: ADAS, AFlow, ScoreFlow, and A2Flow

If DSPy makes workflows programmable, the next question is natural:

> **Can we automatically discover better workflows instead of designing them by hand?**

This is the core idea behind automated workflow search. Instead of manually choosing every module, tool call, agent role, and connection, these methods treat the workflow itself as something that can be generated, searched, scored, and improved.

[ADAS](https://arxiv.org/abs/2408.08435) gives this direction a clear name: **Automated Design of Agentic Systems**. Its key idea is that a meta-agent can design better agents by writing and revising code. This changes the role of the LLM from only solving a task to also designing the system that solves the task.

[AFlow](https://arxiv.org/abs/2410.10762) makes the search process more explicit. It represents workflows as code, where LLM-invoking nodes are connected by edges, and uses Monte Carlo Tree Search to explore better workflow structures. In this view, an agentic workflow becomes a search space.

[ScoreFlow](https://arxiv.org/abs/2502.04306) takes another route. Instead of relying only on discrete search over workflow candidates, it formulates workflow optimization in a more continuous, score-based way. The main motivation is to make workflow optimization more scalable and adaptable.

[A2Flow](https://arxiv.org/abs/2511.20693) pushes the automation further by reducing the need for manually predefined operators. It extracts reusable abstraction operators and uses operator memory to support workflow construction. This makes the search less dependent on fixed human-designed building blocks.

![From Manual Workflow Design to Automated Workflow Search](/images/blogs/the_workflow_turn_in_llm_agents/automated-workflow-search.png)

*Figure 5. Automated workflow search treats the workflow itself as an object to generate, evaluate, and improve. ADAS, AFlow, ScoreFlow, and A2Flow differ mainly in how they represent and optimize this search space.*

A compact way to compare these methods is:

| Method | Main idea | What changes? |
|---|---|---|
| ADAS | A meta-agent designs agentic systems in code | The agent becomes a system designer |
| AFlow | MCTS searches over code-represented workflows | Workflow design becomes tree search |
| ScoreFlow | Score-based preference optimization improves workflows | Search becomes more continuous and feedback-driven |
| A2Flow | Self-adaptive abstraction operators guide workflow generation | Building blocks become more automatic and reusable |

Together, these papers mark an important shift. The question is no longer only:

> **How should we design an agentic workflow?**

but also:

> **How can a system search for a better workflow by itself?**

This is a major step in the workflow landscape. ReAct, ReWOO, and Reflexion define useful control patterns. DSPy makes workflows programmable. Multi-agent methods make collaboration explicit. Automated workflow search then asks whether the structure of the whole system can be optimized, not just the prompts inside it.

# 7. Query-Adaptive Orchestration: MaAS and DAAO

The methods in the previous section search for better workflows. But they still raise another question:

> **Should every query use the same workflow?**

In many cases, the answer is probably no. A simple factual question may not need a deep multi-agent workflow. A hard reasoning problem may need more steps, stronger models, or more agents. This leads to a new direction: **query-adaptive orchestration**.

[MaAS](https://arxiv.org/abs/2502.04180) argues against using one fixed multi-agent system for all inputs. Instead, it introduces an *agentic supernet*: a large space of possible multi-agent architectures. For each query, the system samples a suitable architecture from this supernet. The goal is not only to improve accuracy, but also to allocate computation more efficiently.

[Difficulty-Aware Agentic Orchestration (DAAO)](https://arxiv.org/abs/2509.11079) takes a similar motivation from the perspective of query difficulty. It estimates how difficult a query is, then adapts the workflow depth, operator choice, and model assignment. Easy queries can use cheaper or simpler paths, while harder queries can receive more powerful reasoning workflows.

![Query-Adaptive Workflow Orchestration](/images/blogs/the_workflow_turn_in_llm_agents/query-adaptive-orchestration.png)

*Figure 6. Query-adaptive orchestration selects different workflows for different inputs. Instead of using one fixed agentic system, methods such as MaAS and DAAO adapt the architecture, model choice, workflow depth, or computation budget based on the query.*

This is an important shift in the landscape. Earlier methods ask:

> **What is a good workflow for this task?**

Query-adaptive methods ask a more fine-grained question:

> **What is a good workflow for this specific query?**

This changes the design objective. The best system is not necessarily the largest or most complex one. It is the one that uses the right amount of computation for the current input.

In short, MaAS and DAAO move workflow engineering from **static design** to **adaptive orchestration**. The workflow is no longer only something we design once. It becomes something the system can choose or adjust at inference time.

# 8. The Full Landscape Map

We can now place the papers on a single map.

One useful way to read this area is through two questions:

> **Who designs the workflow?**  
> Human designers, programmers, search algorithms, or meta-agents?

and:

> **When is the workflow chosen?**  
> Once for the whole task, or dynamically for each query?

This gives us a simple landscape.

![A Landscape of LLM Agent Workflows](/images/blogs/the_workflow_turn_in_llm_agents/llm-agent-workflow-landscape.png)

*Figure 7. A high-level landscape of LLM agent workflow research. The field is moving from manually designed prompting loops toward programmable, searchable, and query-adaptive workflow systems.*

On this map, **ReAct**, **ReWOO**, and **Reflexion** sit close to the origin of agentic workflows. They define basic control patterns: act step by step, plan before execution, or improve through reflection.

**DSPy** moves the field toward programmable workflows. It does not only ask what prompt to write, but how to define modules, signatures, metrics, and optimization procedures.

**Mixture-of-Agents** and **GPTSwarm** shift the focus from one agent to many agents. Here, the workflow is not only a sequence of steps, but a collaboration structure.

**ADAS**, **AFlow**, **ScoreFlow**, and **A2Flow** move further to the right. They treat workflows as objects that can be generated, searched, scored, and refined.

Finally, **MaAS** and **DAAO** move upward toward query-adaptive orchestration. They suggest that a workflow should not always be fixed in advance. Instead, the system may choose different architectures, models, operators, or budgets depending on the input.

The map is not meant to be a strict taxonomy. Many papers could belong to more than one region. But it helps show the larger trend:

> **LLM agents are evolving from fixed prompted loops into adaptive workflow systems.**

# 9. A Compact Taxonomy of LLM Agent Workflows

The landscape above can be summarized as a compact taxonomy. Instead of classifying papers only by year or task, we can classify them by the **main workflow question** they address.

| Layer | Main question | Representative methods |
|---|---|---|
| **Control pattern** | How should one agent reason, act, observe, or reflect? | ReAct, ReWOO, Reflexion |
| **Programmable workflow** | How can LM pipelines be represented and optimized as programs? | DSPy |
| **Multi-agent collaboration** | How should multiple agents or models communicate and aggregate outputs? | Mixture-of-Agents, GPTSwarm |
| **Workflow search** | Can better agentic workflows be generated, searched, scored, or refined automatically? | ADAS, AFlow, ScoreFlow, A2Flow |
| **Adaptive orchestration** | Can the system choose different workflows, models, or budgets for different queries? | MaAS, DAAO |

This taxonomy is not meant to be final. Some methods naturally belong to more than one layer. However, it gives a useful way to read the field: each layer changes the unit of design.

The progression is:

> **prompt → control loop → programmable workflow → multi-agent structure → searched workflow → adaptive orchestration**

This is the core of the workflow turn in LLM agents.

# 10. Key Tensions and Open Questions

Looking across the landscape, the papers are not only proposing different methods. They are also exposing several deeper tensions in how LLM agent systems should be built.

| Tension | Core question |
|---|---|
| **Interleaved acting vs. plan-then-execute** | Should an agent reason and act step by step, or make a plan before execution? |
| **Single-agent vs. multi-agent** | When do multiple agents actually help, and when do they only add cost and complexity? |
| **Manual design vs. workflow search** | Should workflows be written by humans, programmed as modules, or discovered automatically? |
| **Fixed workflow vs. query-adaptive workflow** | Should every query use the same workflow, or should the system adapt its strategy per input? |
| **Accuracy vs. cost** | How much extra computation is worth paying for a better answer? |
| **Interpretability vs. search complexity** | As workflows become automatically searched, can humans still understand why a system works? |

These tensions suggest that the field is still far from settled. In fact, many of the most important questions are not only about building stronger agents, but about building agents that are **controllable, efficient, interpretable, and reusable**.

A few open questions seem especially important:

1. **How well do searched workflows generalize?**  
   A workflow may perform well on a development benchmark, but fail when the task distribution changes. This is a major concern for automated workflow search.

2. **How much data is needed to optimize a workflow?**  
   Methods that search, score, or adapt workflows often need evaluation feedback. But in real applications, labeled development data may be small, noisy, or unavailable.

3. **What is the right representation for a workflow?**  
   Workflows can be represented as prompts, programs, graphs, modules, operators, or supernets. Each representation makes some things easier and other things harder.

4. **When is multi-agent collaboration actually useful?**  
   More agents do not automatically mean better reasoning. The key question is when collaboration improves quality enough to justify the extra cost.

5. **Can adaptive systems avoid overthinking easy queries?**  
   Query-adaptive orchestration is attractive because not every input deserves the same expensive workflow. But this requires reliable difficulty estimation and routing.

6. **How should we evaluate agentic workflows?**  
   Accuracy alone is not enough. We may also need to measure cost, latency, robustness, interpretability, and how often the workflow fails silently.

These questions point to the same conclusion: workflow engineering is not just about creating larger or more complicated agent systems. It is about finding the right structure for the right problem under the right constraints.

# 11. Conclusion: From Prompt Engineering to Workflow Engineering

The main story behind these papers is not simply that LLM agents are getting more complex. It is that the **unit of design is changing**.

Early systems focused on prompting a model to produce a better answer. Then agentic methods such as ReAct, ReWOO, and Reflexion showed that the model could follow a control pattern: reason, act, observe, plan, or reflect. DSPy then pushed the field toward programmable workflows. Multi-agent methods such as Mixture-of-Agents and GPTSwarm made collaboration structure explicit. More recent methods such as ADAS, AFlow, ScoreFlow, and A2Flow treat workflows as objects that can be searched and optimized. Finally, MaAS and DAAO suggest that the workflow itself can be selected or adapted for each query.

This gives us a useful way to understand the field:

> **LLM agents are moving from better prompts to better workflow systems.**

The next generation of agentic systems may not be defined by a single powerful prompt or a single powerful model. Instead, they may be defined by how well they can choose the right reasoning path, tool use, model, agent collaboration pattern, and computation budget for the problem at hand.

In other words, the future of LLM agents may depend less on asking:

> **What should the model say?**

and more on asking:

> **What workflow should the system execute?**

That is the workflow turn in LLM agents.

# How to Cite This Post

If you find this survey useful, you can cite or reference it as:

```bibtex
@misc{do2026workflowturn,
  title        = {From ReAct to AFlow: The Workflow Turn in LLM Agents},
  author       = {Phong Do},
  year         = {2026},
  howpublished = {\url{https://phongntdo.github.io/From-ReAct-to-AFlow-The-Workflow-Turn-in-LLM-Agents}},
  note         = {Blog post}
}
```

***Author: Felix Do***