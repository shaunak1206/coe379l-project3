# Use of AI

[1]. Tool: ChatGPT
     Prompt: "Which small language model from Hugging Face would be best for fine-tuning on a text summarization task? I need something that works well but is small enough to train on a single GPU."
     Output: Suggested using `google/flan-t5-small` because it's instruction-tuned, performs well on summarization tasks, and is small enough for efficient fine-tuning on a single GPU. This recommendation was used to select the model for the project.

[2]. Tool: ChatGPT
     Prompt: "How do I calculate ROUGE scores for evaluating text summarization? What's the standard way to use the evaluate library?"
     Output: Provided code example showing how to use `evaluate.load("rouge")` and compute ROUGE-1, ROUGE-2, and ROUGE-L scores. The code structure for the `compute_metrics` function in the notebook was based on this guidance.

[3]. Tool: ChatGPT
     Prompt: "I'm getting an OverflowError when decoding token IDs in my compute_metrics function. The error says 'out of range integral type conversion attempted'. How do I fix this?"
     Output: Suggested clipping token IDs to valid vocabulary range using `np.clip(predictions, 0, vocab_size - 1)` before decoding. This fix was implemented in the `compute_metrics` function to prevent the overflow error.

[4]. Tool: ChatGPT
     Prompt: "What's the best way to evaluate catastrophic forgetting in a fine-tuned language model? Should I test it on general knowledge questions?"
     Output: Recommended creating a small set of factual questions that the base model can answer, then comparing accuracy before and after fine-tuning. Suggested using simple questions about geography, science, and basic facts. This approach was used to design the forgetting analysis section.

[5]. Tool: ChatGPT
     Prompt: "How do I properly format prompts for FLAN-T5 model? What prompt style works best for question answering tasks?"
     Output: Suggested using the format "Question: {question}\nAnswer:" for FLAN-T5, as it's instruction-tuned and responds well to this structured format. This prompt format was used in the `evaluate_forgetting` function.
