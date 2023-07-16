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
- **Reinforcement learning**: GOLD [[5](#5)]
- Using **guidance** from another model to improve the current model: GSum [[6](#6)]
- **Meta-learning** to learn from different systems: RefSum [[7](#7)]
- **Contrastive learning** (most popular type of approach):
  - **Contrastive loss**: SeqCo [[10](#10)]
  - **Ranking loss**: ConSum [[8](#8)], SimCLS [[9](#9)], BRIO [[12](#12)], SLiC [[13](#13)], BalSum [[15](#15)]
  - **Binary cross-entropy loss**: SummaReranker [[11](#11)]
  - **Expected reward**: SLiC [[13](#13)]
  - Mix of **ranking loss + coarse-grained contrastive learning**: SimMCS [[16](#16)]
* **Fusion** of several summary candidates: SummaFusion [[14](#14)]
* **Linear regression** with summary candidates features: SummScore [[17](#17)]

We highlight that some of these models just train the 2nd stage model, which cannot generate summaries by itself and must also rely on 1st stage model summary generation at inference.  
This is the case of: SimCLS [[9](#9)], SummaReranker [[11](#11)], BRIO-ctr [[12](#12)], BalSum [[15](#15)], SummScore [[17](#17)]  
While other approaches perform multi-task learning, where the model is still fine-tuned with the NLL loss.  
This is the case of: ConSum [[8](#8)], SeqCo [[10](#10)], BRIO-mul [[12](#12)], SLiC [[13](#13)], SummaFusion [[14](#14)], SimMCS [[16](#16)]

## Results on CNN-DailyMail

We first show results with baseline (1st stage) models:

| **Model**                 | **R-1** | **R-2** | **R-L** |
|---------------------------|---------|---------|---------|
| T5 (large) [[1](#1)]      | 42.50   | 20.68   | 39.75   |
| BART (large) [[2](#2)]    | 44.16   | 21.28   | 40.90   |
| PEGASUS (large) [[3](#3)] | 44.17   | 21.47   | 41.11   |
| ProphetNet [[4](#4)]      | 43.68   | 20.64   | 40.72   |

And now results with 2-stage models:

| **Model**                | **Base Model** | **R-1** | **R-2** | **R-L** |
|--------------------------|----------------|---------|---------|---------|
| GOLD [[5](#5)]           | BART           | 45.40   | 22.01   | 42.25   |
| GSum [[6](#6)]           | BART           | 45.94   | 22.32   | 42.48   |
| RefSum [[7](#7)]         | _              | 46.18   | 22.36   | 42.91   |
| ConSum [[8](#8)]         | PEGASUS        | 44.53   | 21.54   | 41.57   |
| SimCLS [[9](#9)]         | BART           | 46.67   | 22.15   | 43.54   |
| SeqCo [[10](#10)]          | BART           | 45.02   | 21.80   | 41.75   |
| SummaReranker [[11](#11)]  | PEGASUS        | 47.16   | 22.55   | 43.87   |
| BRIO [[12](#12)]           | BART           | 47.78   | 23.55   | 44.57   |
| SLiC [[13](#13)]           | PEGASUS        | 47.97   | 24.18   | 44.88   |
| BalSum [[15](#15)]       | BART           | 46.58   | 22.33   | 43.49   |
| SimMCS [[16](#16)]       | BART           | 48.38   | 24.17   | 44.79   |

We see that 2nd-stage methods have moved the SOTA from ~44 R-1 to ~48 R-1 in a couple years.


## References
1. <a id="1"></a> Colin Raffel, Noam Shazeer, Adam Roberts, Katherine Lee, Sharan Narang, Michael Matena, Yanqi Zhou, Wei Li, Peter J. Liu. [Exploring the Limits of Transfer Learning with a Unified Text-to-Text Transformer (**T5**)](https://arxiv.org/pdf/1910.10683.pdf). JMLR 2020.  
2. <a id="2"></a> Mike Lewis, Yinhan Liu, Naman Goyal, Marjan Ghazvininejad, Abdelrahman Mohamed, Omer Levy, Ves Stoyanov, Luke Zettlemoyer. [**BART**: Denoising Sequence-to-Sequence Pre-training for Natural Language Generation, Translation, and Comprehension](https://arxiv.org/pdf/1910.13461.pdf). ACL 2020.  
3. <a id="3"></a> Jingqing Zhang, Yao Zhao, Mohammad Saleh, Peter J. Liu. [**PEGASUS**: Pre-training with Extracted Gap-sentences for Abstractive Summarization](https://arxiv.org/pdf/1912.08777.pdf). ICML 2020.  
4. <a id="4"></a> Weizhen Qi, Yu Yan, Yeyun Gong, Dayiheng Liu, Nan Duan, Jiusheng Chen, Ruofei Zhang, Ming Zhou. [**ProphetNet**: Predicting Future N-gram for Sequence-to-Sequence Pre-training](https://arxiv.org/pdf/2001.04063.pdf). EMNLP Findings 2020.  
5. <a id="5"></a> Richard Yuanzhe Pang, He He. [Text Generation by Learning from Demonstrations (**GOLD**)](https://arxiv.org/pdf/2009.07839.pdf). ICLR 2021.
6. <a id="6"></a> Zi-Yi Dou, Pengfei Liu, Hiroaki Hayashi, Zhengbao Jiang, Graham Neubig. [**GSum**: A General Framework for Guided Neural Abstractive Summarization](https://arxiv.org/pdf/2010.08014.pdf). NAACL 2021.  
7. <a id="7"></a> Yixin Liu, Zi-Yi Dou, Pengfei Liu. [**RefSum**: Refactoring Neural Summarization](https://arxiv.org/pdf/2104.07210.pdf). NAACL 2021.  
8. <a id="8"></a> Shichao Sun, Wenjie Li. [Alleviating Exposure Bias via Contrastive Learning for Abstractive Text Summarization (**ConSum**)](https://arxiv.org/pdf/2108.11846.pdf).
9. <a id="9"></a> Yixin Liu, Pengfei Liu. [**SimCLS**: A Simple Framework for Contrastive Learning of Abstractive Summarization](https://arxiv.org/pdf/2106.01890.pdf). ACL 2021.
10. <a id="10"></a> Shusheng Xu, Xingxing Zhang, Yi Wu, Furu Wei. [Sequence Level Contrastive Learning for Text Summarization (**SeqCo**)](https://arxiv.org/pdf/2109.03481.pdf). AAAI 2022.
11. <a id="11"></a> Mathieu Ravaut, Shafiq Joty, Nancy F. Chen. [**SummaReranker**: A Multi-Task Mixture-of-Experts Re-ranking Framework for Abstractive Summarization](https://arxiv.org/pdf/2203.06569.pdf). ACL 2022.  
12. <a id="12"></a> Yixin Liu, Pengfei Liu, Dragomir Radev, Graham Neubig. [**BRIO**: Bringing Order to Abstractive Summarization](https://arxiv.org/pdf/2203.16804.pdf). ACL 2022.
13. <a id="13"></a> Yao Zhao, Misha Khalman, Rishabh Joshi, Shashi Narayan, Mohammad Saleh, Peter J Liu. [Calibrating Sequence Likelihood Improves Conditional Language Generation (**SLiC**)](https://arxiv.org/pdf/2210.00045.pdf). ICLR 2023.
14. <a id="14"></a> Mathieu Ravaut, Shafiq Joty, Nancy F. Chen. [Towards Summary Candidates Fusion (**SummaFusion**)](https://arxiv.org/abs/2210.08779). EMNLP 2022.
15. <a id="15"></a> Jeewoo Sul and Yong Suk Choi. [Balancing Lexical and Semantic Quality in Abstractive Summarization (**BalSum**)](https://arxiv.org/pdf/2305.09898.pdf). ACL 2023.
16. <a id="16"></a> Jiawen Xie, Qi Su, Shaoting Zhang, Xiaofan Zhang. [Alleviating Exposure Bias via Multi-level Contrastive Learning and Deviation Simulation in Abstractive Summarization (**SimMCS**)](https://aclanthology.org/2023.findings-acl.617.pdf). ACL 2023.
17. <a id="17"></a> Mathieu Ravaut, Shafiq Joty, Nancy Chen. [Unsupervised Summarization Re-ranking (**SummScore**)](https://arxiv.org/pdf/2212.09593.pdf). ACL Findings 2023.
