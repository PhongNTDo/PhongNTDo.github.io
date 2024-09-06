---
title: "The Impacts of Unanswerable Questions on the Robustness of Machine Reading Comprehension Models"
collection: publications
category: conferences
permalink: /publication/the-impacts-of-unanswerable-questions-on-the-robustness-of-machine-reading-comprehension-models
excerpt: 'Son Quoc Tran, **Phong Nguyen-Thuan Do**, Uyen Le, Matt Kretchmar. Published at **EACL 2023** [Paper](https://aclanthology.org/2023.eacl-main.113/)'
date: 2023-05-06
paperurl: 'https://aclanthology.org/2023.eacl-main.113/'
---
Pretrained language models have achieved super-human performances on many Machine Reading Comprehension (MRC) benchmarks. Nevertheless, their relative inability to defend against adversarial attacks has spurred skepticism about their natural language understanding. In this paper, we ask whether training with unanswerable questions in SQuAD 2.0 can help improve the robustness of MRC models against adversarial attacks. To explore that question, we fine-tune three state-of-the-art language models on either SQuAD 1.1 or SQuAD 2.0 and then evaluate their robustness under adversarial attacks. Our experiments reveal that current models fine-tuned on SQuAD 2.0 do not initially appear to be any more robust than ones fine-tuned on SQuAD 1.1, yet they reveal a measure of hidden robustness that can be leveraged to realize actual performance gains. Furthermore, we find that robustness of models fine-tuned on SQuAD 2.0 extends on additional out-of-domain datasets. Finally, we introduce a new adversarial attack to reveal of SQuAD 2.0 that current MRC models are learning.