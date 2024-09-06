---
title: "AGent: A Novel Pipeline for Automatically Creating Unanswerable Questions"
collection: publications
category: conferences
permalink: /publication/agent-a-novel-pipeline-for-automatically-creating-unanswerable-questions
excerpt: 'Son Quoc Tran, Gia-Huy Do, **Phong Nguyen-Thuan Do**, Matt Kretchmar, Xinya Du. Preprint arXiv [Paper](https://arxiv.org/pdf/2309.05103)'
date: 2023-09-10
paperurl: 'https://arxiv.org/pdf/2309.05103'
---
The development of large high-quality datasets and high-performing models have led to significant advancements in the domain of Extractive Question Answering (EQA). This progress has sparked considerable interest in exploring unanswerable questions within the EQA domain. Training EQA models with unanswerable questions helps them avoid extracting misleading or incorrect answers for queries that lack valid responses. However, manually annotating unanswerable questions is labor-intensive. To address this, we propose AGent, a novel pipeline that automatically creates new unanswerable questions by re-matching a question with a context that lacks the necessary information for a correct answer. In this paper, we demonstrate the usefulness of this AGent pipeline by creating two sets of unanswerable questions from answerable questions in SQuAD and HotpotQA. These created question sets exhibit low error rates. Additionally, models fine-tuned on these questions show comparable performance with those fine-tuned on the SQuAD 2.0 dataset on multiple EQA benchmarks.