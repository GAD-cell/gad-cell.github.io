---
title: "Luth: Efficient French Specialization for Small Language Models"
description: "A project that addresses the lack of multilingual capabilities in the NLP world."
publishedAt: 2025-08-11
tags: ["javascript", "async", "generators", "streams", "performance"]
---

Large language models have long been trained on predominantly English corpora. This English-centric bias appears in practice: for example, [CroissantLLM](https://huggingface.co/croissantllm) reports that all publicly available LLMs display a large performance disparity between English and non-English languages. A finding we were able to confirm through our own benchmark evaluations. Even widely spoken languages such as French remain underrepresented in the open-source NLP ecosystem, with comparatively fewer high-quality datasets and specialized models.
These imbalances highlight the urgent need to extend robust NLP support to other languages. Recognizing such gaps, recent European initiatives (e.g. [OpenLLM-France](https://huggingface.co/OpenLLM-France)) have emerged to build large French corpora and specialized models, reflecting a growing commitment to language diversity.

In this context, we introduce two compact, non-reasoning causal LLMs, instruction-tuned entirely on French data:
- [Luth-0.6B-Instruct](https://huggingface.co/kurakurai/Luth-0.6B-Instruct)
- [Luth-1.7B-Instruct](https://huggingface.co/kurakurai/Luth-1.7B-Instruct)

<p align="center">
 <img src="https://cdn-uploads.huggingface.co/production/uploads/66beff523ae330ae8b698ee9/jrBZD_72T7W2ut9kgxXlB.png" alt=""  style="width: 90%; height: auto;"><br>
</p>

We also present the [Scholar](https://huggingface.co/datasets/kurakurai/scholar) dataset. Scholar was created to address the lack of high-quality scientific resources in French, drawing from Baccalauréat and Classes Préparatoires (CPGE) exam questions with detailed solutions, and covering a broad range of subjects.
Both Scholar and additional curated French datasets from multiple public sources were combined to form the [Luth-SFT](https://huggingface.co/datasets/kurakurai/luth-sft) dataset, a diverse and high-quality corpus designed for post-training in French instruction following. By focusing fine-tuning entirely on French instructions and dialogues, we confirm that specializing in a lower-resource language does not necessarily compromise overall capability.

By combining targeted fine-tuning with model merging, we achieved, as far as we know, SoTa results for models of this size on several French benchmarks, while maintaining competitive performance and even improving results on some English benchmarks, due to positive cross-lingual transfer. We also provide all code and resources openly on [Github](https://github.com/kurakurai/Luth) to support reproducibility and further research.

# Motivation
Our work confirms that language specialization can be achieved without compromising cross-lingual capabilities. By fine-tuning a model and merging it back with its base version, we significantly improved performance in a target language while maintaining, and in some cases enhancing, English skills. This experiment confirms the potential of model merging for targeted post-training and cross-lingual retention.

We also aimed to provide a high-quality post-training dataset in French, addressing the current lack of open-source specialized resources in the language.

# Evaluation
In order to evaluate the models we have been using **LightEval**. In particular we added custom tasks for the French Benchmarks which are translated versions of some well known benches like Math-500, MMLU, IFEval or GPQA-Diamond.
We also added a patch to add reasoning and hybrid model evaluation support with **LightEval** which is not handled for now by the library.

# Data Mixture
We followed a well-established and effective strategy to build our datasets. Due to the lack of open French post-training datasets, we had to create our own. Our focus was on mathematics, general knowledge, and instruction following. To achieve this, we started from high-quality existing datasets such as **Tulu 3**, which include math and instruction-following tasks.  

Our approach was the following:  
1. **Translate** the prompts into French.  
2. **Generate** new answers for these prompts using a strong multilingual model (mostly Qwen3-32B in our case).  

We also extracted samples from publicly available multilingual datasets on the Hugging Face Hub, including *smoltalk2*, *aya_dataset*, *croissantLLM*, and *OpenHermes-fr*. We applied filtering to ensure data quality and keep it aligned with our target topics.

A notable subset of our data consists of scraped subjects from the French **Baccalauréat** and **Classes Préparatoires aux Grandes Écoles (CPGE)** exams, reformatted into conversational style. This dataset, named [Scholar](https://huggingface.co/datasets/kurakurai/scholar), focuses primarily on mathematics, physics, computer science, and general scientific knowledge.  

By combining all these curated samples, we built [Luth-SFT](https://huggingface.co/datasets/kurakurai/luth-sft), a high-quality French post-training dataset of approximately **338 million tokens**.

# Training
We trained the model using the [Axolotl](https://github.com/axolotl-ai-cloud/axolotl) framework with DeepSpeed for distributed training. The training consisted of full fine-tuning over 3 epochs on the Luth-SFT dataset. Full fine-tuning achieved better performance than LoRA and was not too costly for models of this size. We trained only on the assistant’s outputs using a learning rate of 2e-5, a cosine learning rate scheduler, and a batch size of 24 (with patching).

Below are the training losses for our two models:

<p align="center">
 <img src="https://cdn-uploads.huggingface.co/production/uploads/66beff523ae330ae8b698ee9/hdb87hUEMAQgW6s-RLXO-.png" alt=""  style="width: 80%; height: auto;"><br>
</p>

# Merging
We experimented with several model merging strategies with [MergeKit](https://github.com/arcee-ai/mergekit) to retain the model’s English capabilities. Primarily, we tested SLERP and linear merging with various merging coefficients. For Luth-0.6B-Instruct, SLERP with 70% of the fine-tuned checkpoint yielded the best results, while for Luth-1.7B-Instruct, SLERP with 50% performed best.

Surprisingly, model merging led to even better scores on certain French and English benchmarks compared to both the specialized fine-tuned checkpoint and the original Qwen3 base model. These results confirm that model merging is a powerful and effective approach for targeted post-training on a specific low resource language.

# Benchmarks Results
This method resulted in **Luth-0.6B-Instruct** and **Luth-1.7B-Instruct**, which rank among the best French models of their size for math, instruction following, and general knowledge. Both models also maintain strong English benchmark performance, even outperforming the base Qwen3 model in math and general knowledge. The evaluation was performed with a temperature of 0 and in non-thinking mode.

## **French Benchmark Scores**

| Benchmark         | Qwen3-0.6B       | Qwen2.5-0.5B-Instruct | Luth-0.6B-Instruct |
|-------------------|------------------|-----------------------|-----------------|
| ifeval-fr         | 44.45            | 22.18                 | <u>48.24</u>    |
| gpqa-diamond-fr   | 28.93            | 23.86                 | <u>33.50</u>    |
| mmlu-fr           | 27.16            | 35.04                 | <u>40.23</u>    |
| math-500-fr       | 29.20            | 10.00                 | <u>43.00</u>    |
| arc-chall-fr      | 31.31            | 28.23                 | <u>33.88</u>    |
| hellaswag-fr      | 25.11            | <u>51.45</u>          | 45.70           |


| Benchmark         | Qwen3-1.7B       | SmolLM2-1.7B-Instruct | Qwen2.5-1.5B-Instruct | Luth-1.7B-Instruct   |
|-------------------|------------------|-----------------------|-----------------------|----------------------|
| ifeval-fr         | 54.53            | 31.24                 | 32.90                 | <u>57.67</u>         |
| gpqa-diamond-fr   | 26.90            | 21.83                 | 28.93                 | <u>38.58</u>         |
| mmlu-fr           | 28.46            | 33.73                 | 46.25                 | <u>49.66</u>         |
| math-500-fr       | 60.80            | 11.20                 | 32.20                 | <u>64.00</u>         |
| arc-chall-fr      | 33.28            | 28.57                 | 32.68                 | <u>35.16</u>         |
| hellaswag-fr      | 24.86            | <u>49.58</u>          | 34.34                 | 31.93                |


## **English Benchmark Scores**

| Benchmark         | Qwen3-0.6B       | Qwen2.5-0.5B-Instruct | Luth-0.6B-Instruct   |
|-------------------|------------------|-----------------------|-----------------|
| ifeval-en         | <u>57.86</u>     | 29.21                 | 53.97           |
| gpqa-diamond-en   | <u>29.80</u>     | 26.77                 | 28.28           |
| mmlu-en           | 36.85            | 43.80                 | <u>48.10</u>    |
| math-500-en       | 45.00            | 31.80                 | <u>47.80</u>    |
| arc-chall-en      | 33.62            | 32.17                 | <u>35.92</u>    |
| hellaswag-en      | 42.91            | <u>49.56</u>          | 46.96           |

| Benchmark         | Qwen3-1.7B       | SmolLM2-1.7B-Instruct | Qwen2.5-1.5B-Instruct | Luth-1.7B-Instruct   |
|-------------------|------------------|-----------------------|-----------------------|----------------------|
| ifeval-en         | <u>68.39</u>     | 48.24                 | 39.93                 | 65.80                |
| gpqa-diamond-en   | <u>31.82</u>     | 24.75                 | 30.30                 | <u>31.82</u>         |
| mmlu-en           | 52.74            | 50.27                 | 59.81                 | <u>60.19</u>         |
| math-500-en       | 69.20            | 22.40                 | 56.00                 | <u>70.00</u>         |
| arc-chall-en      | 36.09            | 42.32                 | 41.04                 | <u>42.24</u>         |
| hellaswag-en      | 46.96            | <u>66.94</u>          | 64.48                 | 58.55                |

# Conclusion
Our work shows that we can achieve strong results for a lower-resource language like French without losing performance in English, by combining careful dataset curation with targeted instruction-tuning. By sharing our models and datasets openly, we aim to support further research in multilingual specialization beyond English.

This method can be applied to other underrepresented languages. Using targeted instruction datasets, domain-specific resources, and model merging could help reduce the performance gap for many languages worldwide.
