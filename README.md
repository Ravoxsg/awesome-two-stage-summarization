# Two-stage abstractive summarization

## Standard abstractive summarization
Traditionally, abstractive summarization models are fine-tuned in a single-stage. For instance, one can use a BART, T5 or PEGASUS pre-trained checkpoint, then fine-tune it on the desired dataset. Fine-tuning is done by Maximum Likelihood Estimation (MLE) and negative log-likelihood (NLL) loss, maximizing the probability that the model assigns to the (unique) ground-truth summary paired with the source document. During training, teacher forcing is used, while during inference, auto-regressive decoding is used. 

## Why "two-stage" abstractive summarization 
The above process is not ideal for two main reasons:
1. The model only optimizes for a single summary, while in practice the search space is huge and there exists plenty of high-quality summaries.
2. There is a large discrepancy between teacher forcing during training, and auto-regressive decoding during inference, known as the **exposure bias**. At inference, the model has no mechanism to realize it's decoding in a wrong direction, and cannot change course. Besides, it is never trained to build upon its previous predictions.

Due to these limitations, since around 2021, researchers have started training abstractive summarization in two stages. While the 1st stage is the same (MLE with teacher forcing), the 2nd stage training process differs. The overall goal of the 2nd-stage is to calibrate the generation, so that the model assigns higher probability to summary candidate which actually have a higher performance (e.g., ROUGE or BERTScore with regards to the target). A lot of 2nd stage models operate at the sequence level (in contrast to the 1st stage where the loss is at the token level). 

## Two-stage abstractive summarization techniques
We attempt a broad categorization of two-stage summarization training techniques:
- Using **guidance** from another model to improve the current model: GSum [1]
- **Meta-learning** to learn from different systems: RefSum [2]
- **Contrastive learning**:
  - **Contrastive loss**: SeqCo
  - **Ranking loss**: ConSum, SimCLS, BRIO, SLiC, BalSum
  - **Binary cross-entropy loss**: SummaReranker
  - **Expected reward**: SLiC
  - Mix of **ranking loss + coarse-grained contrastive learning**: SimMCS
* **Fusion** of several summary candidates: SummaFusion

We highlight that some of these models just train the 2nd stage model, which cannot generate summaries by itself and must also rely on 1st stage model summary generation at inference.  
This is the case of: SimCLS, SummaReranker, BRIO-ctr
While other approaches perform multi-task learning, where the model is still fine-tuned with the NLL loss.  
This is the case of: ConSum, SeqCo, BRIO-mul




## Scope
Here we list all of papers related to two-stage neural summarization. Summarization can be extractive or abstractive, we focus on **abstractive** summarization in this repo. The two-stage aspect is defined broadly speaking as any method which involves re-training a base sequence-to-sequence summarization model *which has already been fine-tuned* (using the summary labels from the given dataset). Two-stage models come in different flavors:
* Fine-tuning the model *again*, with a *different loss* (e.g, contrastive learning objective).  
This approach includes: ConSum, SeqCo, BRIO. 
* Using *guidance signal* from another model.  
This approach includes: GSum. 
* Generating sets of summary candidates first, then *re-ranking* them.  
This approach includes: SimCLS, SummaReranker, BRIO.
* *Meta-learning* to learn to select a better candidate.  
This approach includes: RefSum. 
* *Fusion* of several summary candidates to form a new, better one.  
This approach includes: SummaFusion. 
* ...

## List of main (single-stage) summarization papers for reference
1. Mike Lewis, Yinhan Liu, Naman Goyal, Marjan Ghazvininejad, Abdelrahman Mohamed, Omer Levy, Ves Stoyanov, Luke Zettlemoyer. [BART: Denoising Sequence-to-Sequence Pre-training for Natural Language Generation, Translation, and Comprehension](https://arxiv.org/pdf/1910.13461.pdf). ACL 2020.  
CNN/DM results (BART-large): **44.16 R-1 / 21.28 R-2 / 40.90 R-L**
2. Jingqing Zhang, Yao Zhao, Mohammad Saleh, Peter J. Liu. [PEGASUS: Pre-training with Extracted Gap-sentences for Abstractive Summarization](https://arxiv.org/pdf/1912.08777.pdf). ICML 2020.  
CNN/DM results (PEGASUS-large pre-trained on HugeNews): **44.17 R-1 / 21.47 R-2 / 41.11 R-L**
3. Weizhen Qi, Yu Yan, Yeyun Gong, Dayiheng Liu, Nan Duan, Jiusheng Chen, Ruofei Zhang, Ming Zhou. [ProphetNet: Predicting Future N-gram for Sequence-to-Sequence Pre-training](https://arxiv.org/pdf/2001.04063.pdf). EMNLP 2020.  
CNN/DM results: **43.68 R-1 / 20.64 R-2 / 40.72 R-L**

## List of two-stage summarization papers
1. Zi-Yi Dou, Pengfei Liu, Hiroaki Hayashi, Zhengbao Jiang, Graham Neubig. [**GSum**: A General Framework for Guided Neural Abstractive Summarization](https://arxiv.org/pdf/2010.08014.pdf). NAACL 2021.  
CNN/DM results (GSum with MatchSum guidance): **45.94 R-1 / 22.32 R-2 / 42.48 R-L**
2. Yixin Liu, Zi-Yi Dou, Pengfei Liu. [**RefSum**: Refactoring Neural Summarization](https://arxiv.org/pdf/2104.07210.pdf). NAACL 2021.  
CNN/DM results (GSum + RefSum): **46.18 R-1 / 22.36 R-2 / 42.91 R-L**
3. Shichao Sun, Wenjie Li. [Alleviating Exposure Bias via Contrastive Learning for Abstractive Text Summarization (**ConSum**)](https://arxiv.org/pdf/2108.11846.pdf).  
CNN/DM results: **44.53 R-1 / 21.53 R-2 / 41.57 R-L**
4. Yixin Liu, Pengfei Liu. [**SimCLS**: A Simple Framework for Contrastive Learning of Abstractive Summarization](https://arxiv.org/pdf/2106.01890.pdf). ACL 2021.  
CNN/DM results (BART + SimCLS): **46.67 R-1 / 22.15 R-2 / 43.54 R-L**
5. Shusheng Xu, Xingxing Zhang, Yi Wu, Furu Wei. [Sequence Level Contrastive Learning for Text Summarization (**SeqCo**)](https://arxiv.org/pdf/2109.03481.pdf). AAAI 2022.  
CNN/DM results: **45.02 R-1 / 21.80 R-2 / 41.75 R-L**
6. Mathieu Ravaut, Shafiq Joty, Nancy F. Chen. [**SummaReranker**: A Multi-Task Mixture-of-Experts Re-ranking Framework for Abstractive Summarization](https://arxiv.org/pdf/2203.06569.pdf). ACL 2022.  
CNN/DM results (PEGASUS + SummaReranker): **47.16 R-1 / 22.55 R-2 / 43.87 R-L**
7. Yixin Liu, Pengfei Liu, Dragomir Radev, Graham Neubig. [**BRIO**: Bringing Order to Abstractive Summarization](https://arxiv.org/pdf/2203.16804.pdf). ACL 2022.  
CNN/DM results: **47.78 R-1 / 23.55 R-2 / 44.57 R-L**
8. Yusen Zhang, Ansong Ni, Ziming Mao, Chen Henry Wu, Chenguang Zhu, Budhaditya Deb, Ahmed H. Awadallah, Dragomir Radev, Rui Zhang. [**SUMM^N**: A Multi-Stage Summarization Framework for Long Input Dialogues and Documents](https://arxiv.org/pdf/2110.10150.pdf). ACL 2022.  
CNN/DM results: _ (not applied on CNN/DM)
9. Mathieu Ravaut, Shafiq Joty, Nancy F. Chen. [Towards Summary Candidates Fusion (**SummaFusion**)](https://arxiv.org/abs/2210.08779). EMNLP 2022.  
CNN/DM results: _ (not applied on CNN/DM)
