# awesome-two-stage-summarization

### Scope
Here we list all of papers related to two-stage neural summarization. Summarization can be extractive or abstractive. The two-stage aspect is defined broadly speaking as any method which involves re-training a base sequence-to-sequence summarization model *which has already been fine-tuned* (using the summary labels from the given dataset). Two-stage models come in different flavors:
* Fine-tuning the model again, with a *different loss* (e.g, contrastive learning objective).
* Generating summary candidates, then *re-ranking* them. 
* ...

### List of papers
1. Zi-Yi Dou, Pengfei Liu, Hiroaki Hayashi, Zhengbao Jiang, Graham Neubig. [GSum: A General Framework for Guided Neural Abstractive Summarization](https://arxiv.org/pdf/2010.08014.pdf). NAACL 2021.  
CNN/DM results (GSum with MatchSum guidance): 45.94 R-1 / 22.32 R-2 / 42.48 R-L
2. Yixin Liu, Zi-Yi Dou, Pengfei Liu. [RefSum: Refactoring Neural Summarization](https://arxiv.org/pdf/2104.07210.pdf). NAACL 2021.  
CNN/DM results (GSum + RefSum): 46.18 R-1 / 22.36 R-2 / 42.91 R-L
3. Yixin Liu, Pengfei Liu. [SimCLS: A Simple Framework for Contrastive Learning of Abstractive Summarization](https://arxiv.org/pdf/2106.01890.pdf). ACL 2021.  
CNN/DM results (BART + SimCLS): 46.67 R-1 / 22.15 R-2 / 43.54 R-L
4. Shusheng Xu, Xingxing Zhang, Yi Wu, Furu Wei. [Sequence Level Contrastive Learning for Text Summarization](https://arxiv.org/pdf/2109.03481.pdf). AAAI 2022.  
CNN/DM results: 45.02 R-1 / 21.80 R-2 / 41.75 R-L
5. Mathieu Ravaut, Shafiq Joty, Nancy F. Chen. [SummaReranker: A Multi-Task Mixture-of-Experts Re-ranking Framework for Abstractive Summarization](https://arxiv.org/pdf/2203.06569.pdf). ACL 2022.  
CNN/DM results: 47.16 R-1 / 22.55 R-2 / 43.87 R-L
6. Yixin Liu, Pengfei Liu, Dragomir Radev, Graham Neubig. [BRIO: Bringing Order to Abstractive Summarization](https://arxiv.org/pdf/2203.16804.pdf). ACL 2022.  
CNN/DM results: 47.78 R-1 / 23.55 R-2 / 44.57 R-L
