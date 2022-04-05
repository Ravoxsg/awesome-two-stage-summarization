# awesome-two-stage-summarization

### Scope
Here we list all of papers related to two-stage neural summarization. Summarization can be extractive or abstractive. The two-stage aspect is defined broadly speaking as any method which involves re-training a base sequence-to-sequence summarization model *which has already been fine-tuned* (using the summary labels from the given dataset). Two-stage models come in different flavors:
* Fine-tuning the model again, with a *different loss* (e.g, contrastive learning objective).
* Generating summary candidates, then *re-ranking* them. 
* ...

### List of papers
1. Zi-Yi Dou, Pengfei Liu, Hiroaki Hayashi, Zhengbao Jiang, Graham Neubig. [GSum: A General Framework for Guided Neural Abstractive Summarization](https://arxiv.org/pdf/2010.08014.pdf). NAACL 2021.  
CNN/DM results: 45.94 R-1 / 22.32 R-2 / 42.48 R-L
3. Yixin Liu, Zi-Yi Dou, Pengfei Liu. [RefSum: Refactoring Neural Summarization](https://arxiv.org/pdf/2104.07210.pdf). NAACL 2021.
4. Yixin Liu, Pengfei Liu. [SimCLS: A Simple Framework for Contrastive Learning of Abstractive Summarization](https://arxiv.org/pdf/2106.01890.pdf). ACL 2021.
5. Shusheng Xu, Xingxing Zhang, Yi Wu, Furu Wei. [https://arxiv.org/abs/2109.03481](https://arxiv.org/pdf/2109.03481.pdf). AAAI 2022.
