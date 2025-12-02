# Project 3 Results Summary

## Training Results

**Model:** `google/flan-t5-small` fine-tuned on Amazon Fine Food Reviews  
**Training Samples:** 16,000  
**Validation Samples:** 2,000  
**Test Samples:** 2,000  
**Epochs:** 2  
**Batch Size:** 4 per device  
**Learning Rate:** 2e-4

## Summarization Performance (ROUGE Scores)

| Metric | Score (%) |
| :--- | :--- |
| ROUGE-1 | 17.24 |
| ROUGE-2 | 7.06 |
| ROUGE-L | 16.92 |
| ROUGE-Lsum | 16.87 |
| Average Generation Length | 6.62 tokens |

## Forgetting Analysis

| Model | Accuracy | Correct/Total |
| :--- | :--- | :--- |
| Base Model | 25.00% | 2/8 |
| Fine-Tuned Model | 25.00% | 2/8 |
| **Change** | **0.00%** | **No change** |

**Conclusion:** No catastrophic forgetting detected. The fine-tuned model maintained the same general knowledge performance as the base model.

## Key Findings

1. **Summarization Improvement:** The fine-tuned model learned to generate shorter, more concise summaries compared to the base model.

2. **Improved ROUGE Scores:** ROUGE scores around 17% (ROUGE-1) and 7% (ROUGE-2) indicate improved performance compared to initial training. The model captures better unigram and bigram overlap with reference summaries.

3. **No Forgetting:** The model did not show catastrophic forgetting - accuracy on general knowledge questions remained at 25% for both models.

## Model Checkpoint

The fine-tuned model checkpoint is saved to `./finetuned_summarizer_final` in Google Colab (not included in repository due to size).

## Detailed Results

For complete qualitative examples, detailed analysis, and methodology, see `reports/Final_Report.md`.

