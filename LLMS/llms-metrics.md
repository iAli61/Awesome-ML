# Evaluation Metrics:

Evaluating the performance of machine learning models is crucial for determining their effectiveness and reliability. To do that, quantitative measurement with reference to ground truth output (also known as evaluation metrics) are needed. Several metrics have been proposed in the literature for evaluating the performance of LLMs. It is essential to use the right metrics that are suitable for the problem we are attempting to solve.

This document aims to cover several popularly used evaluation metrics and methods that could be useful for evaluating these large models over various Natural Language Processing related tasks.

## Text Similarity Metrics:

### Levenshtein Similarity Ratio

### BLEU: Bilingual Evaluation Understudy

### ROUGE: Recall-Oriented Understudy for Gisting Evaluation


### Semantic Similarity:

### METEOR:

### BERTScore:


### LLM-based Evaluation Metrics:

- [G-EVAL: NLG Evaluation using GPT-4 with Better Human Alignment](https://arxiv.org/pdf/2303.16634.pdf)
  - code: [G-EVAL](https://github.com/nlpyang/geval)



### Human Feedback-based Evaluation Metrics:

Human based feedback is always going to be a good (if not the gold standard) option for evaluating the quality of LLM-generate output. Depending on the application, reviewers with domain expertise may be required. This process can be as simple as having a human verify output and assign it a pass/fail mark. However, for a more refined human evaluation process the following steps can be taken:

- Identifying a group of domain experts or people familiar with the problem space
- Generating a shareable asset that can be viewed by multiple reviewers. For example, a shared excel spreadsheet can be saved for later use in experiments.
- Having each individual review each sample and assigning a score for each completion. Alternatively, reviewers could split up work and review different sets of data points to get through the review process faster.
- Additionally, having notes assigned to each reviewed data point will give an insight as to how each specific reviewer interpreted each completion for each data point. Having notes is useful as it serves as a reference point for users. These users may have not reviewed the dataset before, but want to understand the thought process involved with each review. Notes could also serve as a convention for standardizing the evaluation process. Learnings derived from each reviewed note could potentially be applied to other samples within the dataset.
  
Depending on human resources available, getting human feedback might be a tedious task. Scenarios that might involve this sort of evaluation need to be carefully assessed.


### Rule-based Metrics:

for example, for summarization tasks, we can use the following metrics:

- Syntax correctness
- Grammar correctness
- Format check
- Keyword presence