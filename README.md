# Two-stage abstractive summarization

## Standard abstractive summarization
Traditionally, abstractive summarization models are fine-tuned in a single-stage. For instance, one can use a BART, T5 or PEGASUS pre-trained checkpoint, then fine-tune it on the desired dataset (e.g, CNN/DailyMail). Fine-tuning is done by **Maximum Likelihood Estimation (MLE)** and negative log-likelihood (NLL) loss, maximizing the probability that the model assigns to the (unique) ground-truth summary paired with the source document. During training, **teacher forcing** is used, while during inference, **auto-regressive decoding** is used. 

## Why "two-stage" abstractive summarization 
The above process is not ideal for two main reasons:
1. The model only optimizes for a single summary, while in practice the search space is huge and *there exists plenty of high-quality summaries*.
2. There is a large discrepancy between teacher forcing during training, and auto-regressive decoding during inference, known as the **exposure bias**. At inference, the model has no mechanism to realize it's decoding in a wrong direction, and cannot change course. Besides, it is never trained to build upon its previous predictions.

Due to these limitations, since around 2021, researchers have started training abstractive summarization in *two stages*. While the 1st stage is the same (MLE with teacher forcing), the 2nd stage training process differs. The overall goal of the 2nd-stage is to *calibrate* the generation, so that the model assigns higher probability to summary candidates which actually have a higher performance (e.g., ROUGE or BERTScore with regards to the target). A lot of 2nd stage models operate at the sequence level (in contrast to the 1st stage where the loss is at the token level). 

## Two-stage abstractive summarization techniques
We attempt a broad categorization of two-stage abstractive summarization training techniques:
- Using **guidance** from another model to improve the current model: GSum [1](#1)
- **Meta-learning** to learn from different systems: RefSum [2]
- **Contrastive learning** (most popular type of approach):
  - **Contrastive loss**: SeqCo [5]
  - **Ranking loss**: ConSum [3], SimCLS [4], BRIO [7], SLiC [8], BalSum [10]
  - **Binary cross-entropy loss**: SummaReranker [6]
  - **Expected reward**: SLiC [8]
  - Mix of **ranking loss + coarse-grained contrastive learning**: SimMCS [11]
* **Fusion** of several summary candidates: SummaFusion [9]

We highlight that some of these models just train the 2nd stage model, which cannot generate summaries by itself and must also rely on 1st stage model summary generation at inference.  
This is the case of: SimCLS [4], SummaReranker [6], BRIO-ctr [7], BalSum [10]  
While other approaches perform multi-task learning, where the model is still fine-tuned with the NLL loss.  
This is the case of: ConSum [3], SeqCo [5], BRIO-mul [7], SLiC [8], SummaFusion [9], SimMCS [11] 

## Results on CNN-DailyMail

We first show results with baseline (1st stage) models:

| **Model**       | **R-1** | **R-2** | **R-L** |
|-----------------|---------|---------|---------|
| T5 (large)      | 42.50   | 20.68   | 39.75   |
| BART (large)    | 44.16   | 21.28   | 40.90   |
| PEGASUS (large) | 44.17   | 21.47   | 41.11   |
| ProphetNet      | 43.68   | 20.64   | 40.72   |

And now results with 2-stage models:

| **Model**         | **Base Model** | **R-1** | **R-2** | **R-L** |
|-------------------|----------------|---------|---------|---------|
| GSum [1]          | BART           | 45.94   | 22.32   | 42.48   |
| RefSum [2]        | _              | 46.18   | 22.36   | 42.91   |
| ConSum [3]        | PEGASUS        | 44.53   | 21.54   | 41.57   |
| SimCLS [4]        | BART           | 46.67   | 22.15   | 43.54   |
| SeqCo [5]         | BART           | 45.02   | 21.80   | 41.75   |
| SummaReranker[6]  | PEGASUS        | 47.16   | 22.55   | 43.87   |
| BRIO [7]          | BART           | 47.78   | 23.55   | 44.57   |
| SLiC [8]          | PEGASUS        | 47.97   | 24.18   | 44.88   |
| BalSum [10]       | BART           | 46.58   | 22.33   | 43.49   |
| SimMCS [11]       | BART           | 48.38   | 24.17   | 44.79   |

We see that 2nd-stage methods have moved the SOTA from ~44 R-1 to ~48 R-1 in a couple years.


## References
1. <a id="1"></a> Zi-Yi Dou, Pengfei Liu, Hiroaki Hayashi, Zhengbao Jiang, Graham Neubig. [**GSum**: A General Framework for Guided Neural Abstractive Summarization](https://arxiv.org/pdf/2010.08014.pdf). NAACL 2021.  
2. Yixin Liu, Zi-Yi Dou, Pengfei Liu. [**RefSum**: Refactoring Neural Summarization](https://arxiv.org/pdf/2104.07210.pdf). NAACL 2021.  
3. Shichao Sun, Wenjie Li. [Alleviating Exposure Bias via Contrastive Learning for Abstractive Text Summarization (**ConSum**)](https://arxiv.org/pdf/2108.11846.pdf).
4. Yixin Liu, Pengfei Liu. [**SimCLS**: A Simple Framework for Contrastive Learning of Abstractive Summarization](https://arxiv.org/pdf/2106.01890.pdf). ACL 2021.
5. Shusheng Xu, Xingxing Zhang, Yi Wu, Furu Wei. [Sequence Level Contrastive Learning for Text Summarization (**SeqCo**)](https://arxiv.org/pdf/2109.03481.pdf). AAAI 2022.
6. Mathieu Ravaut, Shafiq Joty, Nancy F. Chen. [**SummaReranker**: A Multi-Task Mixture-of-Experts Re-ranking Framework for Abstractive Summarization](https://arxiv.org/pdf/2203.06569.pdf). ACL 2022.  
7. Yixin Liu, Pengfei Liu, Dragomir Radev, Graham Neubig. [**BRIO**: Bringing Order to Abstractive Summarization](https://arxiv.org/pdf/2203.16804.pdf). ACL 2022.
8. Yao Zhao, Misha Khalman, Rishabh Joshi, Shashi Narayan, Mohammad Saleh, Peter J Liu. [Calibrating Sequence Likelihood Improves Conditional Language Generation (**SLiC**).](https://arxiv.org/pdf/2210.00045.pdf). ICLR 2023.
9. Mathieu Ravaut, Shafiq Joty, Nancy F. Chen. [Towards Summary Candidates Fusion (**SummaFusion**)](https://arxiv.org/abs/2210.08779). EMNLP 2022.
10. Jeewoo Sul and Yong Suk Choi. [Balancing Lexical and Semantic Quality in Abstractive Summarization (**BalSum**)](https://arxiv.org/pdf/2305.09898.pdf). ACL 2023.
11. Jiawen Xie, Qi Su, Shaoting Zhang, Xiaofan Zhang. [Alleviating Exposure Bias via Multi-level Contrastive Learning and Deviation Simulation in Abstractive Summarization (**SimMCS**)](https://aclanthology.org/2023.findings-acl.617.pdf). ACL 2023.
