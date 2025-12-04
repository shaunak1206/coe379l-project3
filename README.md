# Project 3: Fine Tuning a Small Language Model for Summarization and Measuring Forgetting

**Authors:** Shaunak Kapur & Pranav Krishnan  
**Course:** COE 379L 


## Project Overview

🎥 **[Watch the Demo Video](https://drive.google.com/file/d/1yR2gWrXXymmAX_JBzHKyJdX_kWu2ko5q/view?usp=sharing)**

This project explores the effects of fine-tuning a small language model (`google/flan-t5-small`) on a specific task: summarizing product reviews. We aim to:
1.  Improve the model's ability to generate concise, one-sentence summaries of Amazon product reviews.
2.  Measure "catastrophic forgetting" by evaluating whether the fine-tuned model loses its ability to answer general knowledge questions compared to the base model.

## Repository Structure
- `notebooks/`: Contains the Jupyter notebook (`Project3_Summarization_FineTuning.ipynb`) for training and evaluation.
- `reports/`: Contains the project proposal and the final report template.
- `Use_of_AI.md`: Documentation of AI tools used in this project.

## How to Run the Project

The core logic is contained in a Jupyter notebook designed to run on Google Colab (for free GPU access).

### Prerequisites
- A Google account (to access Google Colab).

### Data Setup
The dataset is automatically downloaded from Hugging Face when you run the notebook. No manual download is required. The notebook uses the **Amazon Fine Food Reviews** dataset available at:
- [Hugging Face Dataset](https://huggingface.co/datasets/jhan21/amazon-food-reviews-dataset)

### Running the Notebook
1.  Go to [Google Colab](https://colab.research.google.com/).
2.  Click **File > Upload notebook** and upload `notebooks/Project3_Summarization_FineTuning.ipynb` from this repository.
3.  Go to **Runtime > Change runtime type** and ensure **T4 GPU** is selected.
4.  Click **Runtime > Run all**.
5.  The dataset will be automatically downloaded from Hugging Face when the notebook runs.

### Output
The notebook will produce:
- **Training Metrics:** Loss and ROUGE scores during training.
- **Test Results:** ROUGE-1, ROUGE-2, ROUGE-L scores on the test set.
- **Qualitative Examples:** Side-by-side comparison of summaries from the Base Model vs. Fine-Tuned Model.
- **Forgetting Analysis:** Accuracy scores on a general knowledge QA set before and after training.
- **Saved Model:** A zip file of the fine-tuned model (if code block uncommented).

## Deliverables
- **Code:** This Git repository.
- **Report:** A final report detailing methods and results (see `reports/`).
- **Video:** A presentation of the findings. [Watch the demo video](https://drive.google.com/file/d/1yR2gWrXXymmAX_JBzHKyJdX_kWu2ko5q/view?usp=sharing)

