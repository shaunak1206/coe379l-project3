# Project 3 Final Report: Fine-Tuning FLAN-T5 for Summarization & Measuring Forgetting

**Authors:** Shaunak Kapur & Pranav Krishnan  
**Date:** December 12, 2025

## 1. Introduction and Project Statement

Fine-tuning pre-trained language models has become a standard approach for adapting general-purpose models to specific tasks. However, a critical concern in this process is catastrophic forgetting—the phenomenon where a model loses its ability to perform previously learned tasks when trained on new data. This project investigates both the benefits and potential drawbacks of fine-tuning by adapting a small language model (`google/flan-t5-small`) to generate concise summaries of product reviews while measuring whether this specialization comes at the cost of general knowledge.

Summarization is a fundamental natural language processing task with broad applications in e-commerce, content curation, and information retrieval. Product reviews, in particular, benefit from summarization as they help consumers quickly understand key points without reading lengthy text. However, when a model is fine-tuned exclusively on review summarization, it may lose its ability to answer general knowledge questions or perform other tasks it was originally capable of handling.

This project fine-tuned FLAN-T5-Small on the Amazon Fine Food Reviews dataset to generate one-sentence summaries of product reviews. We evaluated the model's summarization quality using ROUGE metrics and compared its performance on a set of general knowledge questions before and after fine-tuning to assess whether catastrophic forgetting occurred.

## 2. Data Sources and Technologies Used

### Data Source

We used the **Amazon Fine Food Reviews** dataset, available on Hugging Face as `jhan21/amazon-food-reviews-dataset`.

- **Original Size:** ~568,000 reviews spanning over 10 years (up to October 2012)
- **Sampled Size:** 20,000 reviews (for computational efficiency)
- **Fields Used:** 
  - `Text`: The full review text (input)
  - `Summary`: The human-written summary (target label)
- **Preprocessing:** 
  - Filtered out reviews longer than 512 characters
  - Removed rows with missing `Text` or `Summary` fields
- **Data Split:** 80% Train (16,000 samples), 10% Validation (2,000 samples), 10% Test (2,000 samples)
- **Source:** [Hugging Face Dataset](https://huggingface.co/datasets/jhan21/amazon-food-reviews-dataset)

### Technologies

- **Model:** `google/flan-t5-small` (Hugging Face)
  - Architecture: Encoder-decoder transformer
  - Parameters: ~60 million
  - Pre-trained on instruction-following tasks
- **Libraries:** 
  - `transformers` (Hugging Face) - Model loading and training
  - `datasets` - Data loading and preprocessing
  - `evaluate` - ROUGE metric computation
  - `rouge_score` - Summarization evaluation
  - `pytorch` - Deep learning framework
  - `accelerate` - Distributed training support
- **Compute:** Google Colab (T4 GPU, 16GB VRAM)
- **Training Framework:** Hugging Face `Seq2SeqTrainer`

## 3. Methods Employed

### Preprocessing

1. **Data Loading:** Downloaded the dataset directly from Hugging Face using `load_dataset()`
2. **Filtering:** 
   - Removed reviews exceeding 512 characters to manage memory and training time
   - Dropped rows with missing `Text` or `Summary` fields
3. **Sampling:** Randomly sampled 20,000 reviews from the filtered dataset (seed=42) to balance training quality and computational cost
4. **Tokenization:**
   - Added prefix: `"Summarize this review: "` to each input text
   - Maximum input length: 256 tokens
   - Maximum target length: 32 tokens
   - Used T5 tokenizer with appropriate padding and truncation

### Fine-Tuning Strategy

- **Task Type:** Sequence-to-Sequence (Seq2Seq) text generation
- **Training Framework:** Hugging Face `Seq2SeqTrainer` with `Seq2SeqTrainingArguments`
- **Hyperparameters:**
  - **Epochs:** 2
  - **Batch Size:** 4 per device (reduced from 8 due to GPU memory constraints)
  - **Learning Rate:** 2e-4
  - **Optimizer:** AdamW (default)
  - **Weight Decay:** 0.01
  - **Precision:** BF16 (for modern GPUs) / FP32 fallback
  - **Generation Parameters:**
    - Max length: 32 tokens
    - Number of beams: 4
    - Early stopping: Enabled
- **Evaluation Strategy:** Evaluated after each epoch on the validation set
- **Model Saving:** Saved best model based on ROUGE-1 score

### Evaluation Metrics

1. **ROUGE Scores:** 
   - ROUGE-1: Unigram overlap between generated and reference summaries
   - ROUGE-2: Bigram overlap
   - ROUGE-L: Longest Common Subsequence (LCS) based F-score
   - ROUGE-Lsum: Sentence-level LCS F-score
   - All scores reported as percentages (0-100 scale)

2. **Generation Length:** Average number of tokens in generated summaries (excluding padding)

3. **Forgetting Analysis:** 
   - Custom test set of 8 general knowledge questions covering:
     - Geography (capital cities)
     - Basic facts (days in a week, colors)
     - Science (plant biology, chemistry, astronomy)
     - Literature (authors)
     - Mathematics (simple arithmetic)
   - Evaluated both base model and fine-tuned model on the same questions
   - Accuracy calculated as percentage of correct answers
   - Used flexible matching (partial matches, number extraction) to account for model output variations

## 4. Results

### A. Summarization Performance (ROUGE)

| Metric | Test Set Score (%) |
| :--- | :--- |
| ROUGE-1 | 12.64 |
| ROUGE-2 | 2.44 |
| ROUGE-L | 12.41 |
| ROUGE-Lsum | 12.35 |
| Average Generation Length | 7.83 tokens |

**Interpretation:** The ROUGE scores indicate moderate performance. ROUGE-1 and ROUGE-L scores around 12-13% suggest that the model is capturing some unigram overlap with reference summaries, but the low ROUGE-2 score (2.44%) indicates limited bigram overlap, meaning the model struggles to generate exact phrase matches. The average generation length of 7.83 tokens aligns with our target of concise one-sentence summaries.

The model did learn to generate shorter, more focused summaries compared to the base model's tendency to copy longer portions of the input text. However, the relatively low ROUGE scores suggest that fine-tuning on 20,000 samples over 2 epochs may not have been sufficient to fully adapt the model to the summarization task, or that the task itself is challenging given the diversity of review styles and summary formats in the dataset.

### B. Qualitative Comparison

We compared the output of the Base Model vs. the Fine-Tuned Model on 5 unseen test reviews:

**Example 1:**
> **Review:** "This chip has a little tomato taste but the jalepeno seems mild. Good alternative to plain tortilla chips. Not overwhelmed by the taste but a good change...."  
> **Reference:** "Good snack, not very hot"  
> **Base Model:** "This chip has a little tomato taste but the jalepeno seems mild. Good alternative to plain tortilla chips. Not overwhelmed by the taste but"  
> **Fine-Tuned Model:** "A little tomato taste but a little mild"

*Observation:* The fine-tuned model produced a shorter, more concise summary that captures the key points (tomato taste, mild flavor) but loses some nuance. The base model copied a longer portion of the review text, while the fine-tuned model attempted to condense it, though it didn't match the reference summary exactly.

**Example 2:**
> **Review:** "I received my first jar as a gift and promptly fell in love with it! When I found it on this website I was so thrilled. The transaction went very smoothly. Not a problem to be found. I think the pr..."  
> **Reference:** "Best Jam Ever"  
> **Base Model:** "Love it!"  
> **Fine-Tuned Model:** "Yummy!"

*Observation:* Both models generated very short, positive summaries. The fine-tuned model's "Yummy!" is even more concise than the base model's "Love it!", but neither captures the enthusiastic tone of the reference summary "Best Jam Ever."

**Example 3:**
> **Review:** "I love it! Great smell and exceptional taste! I've tried lot of different sorts/tastes of coffee from cheap to exclusively expensive, but this one remains one of my favorite for years.<br />Great Coffe..."  
> **Reference:** "Melitta French Vanilla Ground Coffee"  
> **Base Model:** "Five Stars"  
> **Fine-Tuned Model:** "Love this coffee!"

*Observation:* The fine-tuned model generated a more descriptive summary ("Love this coffee!") compared to the base model's generic "Five Stars", showing some adaptation to the review content. However, neither model captured the product name from the reference.

**Example 4:**
> **Review:** "This product does not taste as sweet as the individual packets made by this same company. Unfortunately they donot sell/package individual packets anymore. So I just add Irish Cream creamer to it to..."  
> **Reference:** "Cappuchino powder"  
> **Base Model:** "This product does not taste as sweet as the individual packets made by this same company. Unfortunately they do not sell/package individual packets anymore."  
> **Fine-Tuned Model:** "Not as sweet as the individual packets made by this company"

*Observation:* The fine-tuned model successfully condensed the base model's longer output into a more concise summary, removing redundancy while preserving the key complaint about sweetness.

**Example 5:**
> **Review:** "Our australian shepard puppies love these! and they are the perfect size for training and to put in their treat balls...."  
> **Reference:** "GREAT"  
> **Base Model:** "Great for training!"  
> **Fine-Tuned Model:** "Great for training!"

*Observation:* Both models produced identical outputs, suggesting that for some reviews, the base model already generates appropriate summaries, and fine-tuning didn't change the behavior.

**Overall Qualitative Assessment:** The fine-tuned model shows a tendency toward shorter, more concise summaries compared to the base model, which often copied longer portions of the input text. However, the summaries don't always match the reference summaries in style or content, indicating room for improvement with more training data or epochs.

### C. Forgetting Analysis

- **Base Model Accuracy:** 25.00% (2/8 questions correct)
- **Fine-Tuned Model Accuracy:** 25.00% (2/8 questions correct)
- **Change in Accuracy:** 0.00%

**Detailed Results:**

| Question | Expected Answer | Base Model Answer | Fine-Tuned Model Answer | Both Correct? |
| :--- | :--- | :--- | :--- | :--- |
| What is the capital of France? | Paris | london | French capital | ✗ ✗ |
| How many days are in a week? | 7 | 7 days | 7 days | ✓ ✓ |
| What gas do plants absorb? | carbon dioxide | helium | gas | ✗ ✗ |
| What is the largest planet? | Jupiter | venus | Earth | ✗ ✗ |
| What is H2O? | water | H2O | H2O | ✗ ✗ |
| Who wrote Romeo and Juliet? | Shakespeare | edward wilson | edmund wilson | ✗ ✗ |
| What color is the sky? | blue | blue | blue sky | ✓ ✓ |
| What is 2 + 2? | 4 | 2 + 2 | 2 + 2 | ✗ ✗ |

**Example of Change:**
> **Question:** "What is the capital of France?"  
> **Base Model Answer:** "london"  
> **Fine-Tuned Model Answer:** "French capital"

*Observation:* The base model incorrectly answered "london" (likely confusing France with England), while the fine-tuned model answered "French capital" (more relevant but still not the exact answer "Paris"). This suggests the fine-tuned model may have slightly improved its understanding of the question format, but both models struggled with factual accuracy.

**Conclusion on Forgetting:** The fine-tuning process did **not** cause catastrophic forgetting in this case. The fine-tuned model maintained the same 25% accuracy on general knowledge questions as the base model. However, this result should be interpreted cautiously:

1. **Low Baseline Performance:** The base model's 25% accuracy is already quite low, suggesting that FLAN-T5-Small may not be well-suited for factual question answering without task-specific fine-tuning.

2. **No Degradation Observed:** The fact that accuracy did not decrease suggests that the fine-tuning on review summarization did not significantly interfere with the model's general knowledge capabilities, at least for this small test set.

3. **Possible Explanations:** 
   - The summarization task and question-answering task may use different parts of the model's knowledge, allowing both to coexist
   - The small test set (8 questions) may not be comprehensive enough to detect subtle forgetting
   - The model's general knowledge performance was already limited, leaving little room for degradation

4. **Limitations:** The forgetting analysis used a small, handcrafted set of questions. A more comprehensive evaluation with a larger, diverse set of general knowledge questions would provide stronger evidence about forgetting behavior.

## 5. Discussion and Limitations

### Key Findings

1. **Summarization Improvement:** The fine-tuned model learned to generate shorter, more concise summaries compared to the base model, demonstrating task-specific adaptation.

2. **Moderate ROUGE Scores:** ROUGE scores around 12-13% indicate room for improvement, possibly requiring more training data, more epochs, or different hyperparameters.

3. **No Catastrophic Forgetting:** The model maintained its (limited) general knowledge performance after fine-tuning, suggesting that task-specific fine-tuning did not significantly degrade other capabilities.

### Limitations

1. **Small Training Set:** Using only 20,000 samples may have limited the model's ability to fully learn the summarization task.

2. **Limited Epochs:** Training for only 2 epochs may not have been sufficient for convergence.

3. **Small Forgetting Test Set:** The 8-question test set is too small to draw definitive conclusions about forgetting behavior.

4. **Model Size:** FLAN-T5-Small is a relatively small model (60M parameters), which may limit its capacity for both summarization and general knowledge.

5. **Evaluation Metrics:** ROUGE scores, while standard, may not fully capture the quality of summaries, especially for very short outputs.

### Future Work

1. **Increase Training Data:** Train on the full dataset or a larger sample (50,000+ reviews).

2. **Extended Training:** Train for more epochs (3-5) with learning rate scheduling.

3. **Hyperparameter Tuning:** Experiment with different learning rates, batch sizes, and generation parameters.

4. **Comprehensive Forgetting Analysis:** Use a larger, more diverse set of general knowledge questions or a standardized benchmark.

5. **Larger Model:** Experiment with FLAN-T5-Base or FLAN-T5-Large to see if model capacity affects both summarization quality and forgetting behavior.

## 6. References

1. Hugging Face Model Card: [google/flan-t5-small](https://huggingface.co/google/flan-t5-small)

2. Amazon Fine Food Reviews Dataset: [jhan21/amazon-food-reviews-dataset](https://huggingface.co/datasets/jhan21/amazon-food-reviews-dataset)

3. Lin, C. Y. (2004). ROUGE: A Package for Automatic Evaluation of Summaries. In *Text Summarization Branches Out* (pp. 74-81).

4. Chung, H. W., et al. (2022). Scaling Instruction-Finetuned Language Models. *arXiv preprint arXiv:2210.11416*.

5. McAuley, J., & Leskovec, J. (2013). From amateurs to connoisseurs: modeling the evolution of user expertise through online reviews. In *Proceedings of the 22nd international conference on World Wide Web* (pp. 897-908).

6. Hugging Face Transformers Library: [transformers](https://github.com/huggingface/transformers)

7. Google Colab: [colab.research.google.com](https://colab.research.google.com/)

