# Project 3 Final Report: Fine-Tuning FLAN-T5 for Summarization

**Authors:** Shaunak Kapur & Pranav Krishnan  
**Date:** December 12, 2025

## 1. Introduction and Project Statement
[Paste the introduction from your proposal here. Elaborate on *why* summarization is important and *why* studying forgetting matters. Briefly mention that you fine-tuned FLAN-T5-Small on Amazon reviews.]

## 2. Data Sources and Technologies Used
### Data Source
We used the **Amazon Fine Food Reviews** dataset.
- **Size:** ~568,000 reviews (Sampled down to 20,000 for training).
- **Fields Used:** `Text` (Review body) and `Summary` (Target label).
- **Link:** [https://www.kaggle.com/datasets/snap/amazon-fine-food-reviews](https://www.kaggle.com/datasets/snap/amazon-fine-food-reviews)

### Technologies
- **Model:** `google/flan-t5-small` (Hugging Face).
- **Libraries:** `transformers`, `datasets`, `evaluate`, `rouge_score`, `pytorch`.
- **Compute:** Google Colab (T4 GPU).

## 3. Methods Employed
### Preprocessing
- Filtered out reviews longer than 512 characters.
- Added prefix: `"Summarize this review: "`.
- Split data: 80% Train, 10% Validation, 10% Test.

### Fine-Tuning Strategy
- **Task:** Sequence-to-Sequence (Seq2Seq).
- **Trainer:** Hugging Face `Seq2SeqTrainer`.
- **Hyperparameters:**
  - Epochs: 2
  - Batch Size: 8
  - Learning Rate: 2e-4
  - Optimizer: AdamW

### Evaluation Metrics
1.  **ROUGE Scores:** Measuring overlap (unigram, bigram, LCS) between generated and reference summaries.
2.  **Forgetting Analysis:** A custom test set of 8 general knowledge questions (e.g., "What is the capital of France?") evaluated before and after training.

## 4. Results

### A. Summarization Performance (ROUGE)
| Metric | Score (%) |
| :--- | :--- |
| ROUGE-1 | [INSERT VALUE FROM NOTEBOOK] |
| ROUGE-2 | [INSERT VALUE FROM NOTEBOOK] |
| ROUGE-L | [INSERT VALUE FROM NOTEBOOK] |

*Interpretation:* [Discuss whether these scores indicate good performance. Did the model learn to copy the style of the dataset?]

### B. Qualitative Comparison
We compared the output of the Base Model vs. the Fine-Tuned Model on unseen test reviews.

**Example 1:**
> **Review:** [Insert Review Text]  
> **Reference:** [Insert Ground Truth]  
> **Base Model:** [Insert Output]  
> **Fine-Tuned Model:** [Insert Output]

*Observation:* [Did the fine-tuned model become more concise? Did it fix grammar?]

### C. Forgetting Analysis
- **Base Model Accuracy:** [INSERT %]
- **Fine-Tuned Model Accuracy:** [INSERT %]

**Example of Change:**
> **Question:** "What is the capital of France?"  
> **Base Model Answer:** "Paris"  
> **Fine-Tuned Model Answer:** [Did it answer "Great product"? or something relevant?]

*Conclusion on Forgetting:* [Did the model forget general knowledge? If accuracy dropped significantly, catastrophic forgetting occurred. If it stayed the same, the model retained its knowledge.]

## 5. References
1.  Hugging Face Model Card: [google/flan-t5-small](https://huggingface.co/google/flan-t5-small)
2.  Amazon Fine Food Reviews Dataset (Kaggle).
3.  ROUGE: A Package for Automatic Evaluation of Summaries.

