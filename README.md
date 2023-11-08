# Archive Notice
This repository has been archived due to the nature of the work being superseded by OpenAI's Advanced Data Analysis tool built into ChatGPT. That product is far better than I could hope to achieve in the time I had for this project, and so this work is not particularly relevant anymore.

# DataScienceLLM
Use LLMs for Data Science Analysis

<!-- ------------------------------------------------------------------------- -->
# The Problem

### Motivation
Data science and statistical analyses are difficult to accomplish, especially on complex and novel data sets. Large datasets with complex structures require large amounts of time to understand before it is even possible to begin analyzing the data within them.

### Problem Statement
The goal of this projects is to develop an application that leverages large language models (LLMS) to assist in data science and statistical analyses. The application will be able to accept a data file in the form of a CSV and a question from the user about some analysis on the data and return a response to the user. The results should answer the question posed by the user as closely as possible, and perform any mathematical operations accurately.

The resulting interface will be intuitive for less experienced data scientists and students, allowing for quick exploratory analysis on data sets. The program should be able to assist these data scientists in performing analyses that they may not have the experience to perform themselves, and on data where they may not have quite the same level of domain knowledge as a more experienced data scientist. LLMs seem to excel at understanding the language around a problem and pulling from training on a multitude of domains, so they bring the advantage to a user of not needed to know as much about the domain they are working in.

<!-- ------------------------------------------------------------------------- -->
# Literature Review

### Pure ChatGPT
First I tried running some simple analysis through ChatGPT with no plugins. As expected, it failed completely to do simple math. For example, providing it the following table and asking it to produce an average sepal length, it returned 6.54, instead of the correct 6.02. It was correctly able to identify how to calculate an average, but summed just 10 numbers incorrectly. For larger datasets, this would likely be even worse. In testing this, I used the system prompt: "You are a data analyst chat bod designed to run accurate statistical analyses on the provided markdown datasets." and prompted the model with the below table and the question "Given this dataset, what is the average sepal length?"

| Sepal Length  | Sepal Width  | Petal Length  | Petal Width  |
|---------------|--------------|---------------|--------------|
| 6.5           | 3.0          | 5.2           | 2.0          |
| 4.8           | 3.4          | 1.6           | 0.2          |
| 5.4           | 3.4          | 1.7           | 0.2          |
| 6.7           | 3.3          | 5.7           | 2.5          |
| 5.7           | 3.0          | 4.2           | 1.2          |
| 7.6           | 3.0          | 6.6           | 2.1          |
| 6.7           | 3.1          | 4.4           | 1.4          |
| 6.3           | 2.3          | 4.4           | 1.3          |
| 6.1           | 2.9          | 4.7           | 1.4          |
| 4.4           | 3.0          | 1.3           | 0.2          |

### Defog.ai
An article at [Defog.AI](https://defog.ai/blog/llm-data-analysis/) referenced a similar idea, looking into whether LLMs can reliable perform analyses. They found that some basic analyses like the ones failed in the previous example *could* be performed accurately, though methodologies were not discussed. Though I couldn't reproduce the quality of their results, some interesting points about being more specific with prompting and getting the model to use different techniques posed some interesting insight on the problem.

### Papers

- [Numeracy from Literacy: Data Science as an Emergent Skill From Large Language Models](https://arxiv.org/pdf/2301.13382.pdf)
    - Appendix A shows examples of attempting to get ChatGPT, with similar incorrect results much of the time to what I found.
    - Like my own findings, the language model appears to do a good job of determining what type of analysis to perform, but fails to perform the analysis correctly.
    - The authors did seem to have more luck with the model executing python code much better, so it's possible that taking a step-by-step approach with code generation first, then execution could provide more reliable results.
    - Appendix E also illustrates potentially better performance if code is first "executed" on the dataset, and then natural language questions are asked, providing another possible avenue for improvement.
- [TABERT: Pretraining for Joint Understanding of Textual and Tabular Data](https://aclanthology.org/2020.acl-main.745.pdf)
    - This paper introduces a pre-trained model called TaBERT that is trained to handle tabular data in a novel way.
    - Though the paper does not describe running statistical analysis on datasets using the model, the [model itself](https://github.com/facebookresearch/TaBERT) could be useful in developing a more complex strategy for this project.

### Conclusion
Though there is some existing research in the concepts of using LLMs for data science applications, limited research was found into improving the use of these tools for relatively inexperienced data scientists and students. The existing research does show that it is possible to use LLMs to perform some basic statistical analyses, but that the results are not always accurate. The research also shows that it is possible to use LLMs to generate code that can be executed on the data, which may provide a more reliable method of performing analyses.

<!-- ------------------------------------------------------------------------- -->
# Interface



<!-- ------------------------------------------------------------------------- -->
# Execution plan

## Prompt Engineering

### 1. Get baseline
- Use ChatGPT to get a baseline for how well it can perform basic analyses without any sort of chaining with langchain or extensive prompt engineering
- Start with basic analyses containing standard summary statistics
    - Mean
    - High
    - Low
    - Standard deviation

### 2. Test the same baseline on a chosen LLM other than ChatGPT
- Use the same system prompts and other prompt engineering techniques to perform the same standard analyses on a different LLM to see if there is any improvement.
- I'm still not using Langchain at this point, just testing what the LLMs can do on their own.

### 3. Introduce Langchain
- Use Langchain to help the LLM perform math better
    - User input
    - Combine with the system prompt and the data
    - Possibly use vector store to get relevant part of the data (dataset might be too big)
    - Get response from LLM 
        - This response should be likely be structured to include instructions, an intermediary representation, or code of the math to be performed, but not the actual math itself
    - Use an alternative engine (like Wolfram Alpha or python) to perform the math
    - Format math output and return to the user

### 4. Introduce more complex analyses
- Visualizations (likely using matplotlib)
    - To accomplish this, output data can likely be piped back to an LLM to generate the code for visualizations
- Correlations
- Grouping
- Missing values
- Outliers
- Classification given new datapoints

## Fine Tuning
- If analysis results are not accurate enough, fine tuning may be necessary to help the LLM get a better understanding of the types of tasks it is being asked to perform

# Baseline
- Summary statistics (mean, min, max, standard deviation) on a modified version of the iris dataset (see `datasets/iris_modified.ods` for the modified dataset)
- System prompt: "You are a data analyst chat bot designed to run accurate statistical analyses on the provided datasets. Answer just with the result of the question and not include any explanation on how that answer was achieved. Ensure you only use the data provided to make your calculations."
- User prompt: "Give me standard summary stats (mean, min, max, and standard deviation)"

### ChatGPT
```
For the "Sepal Length" column:

    Mean: 5.8433
    Minimum: 4.3
    Maximum: 7.9
    Standard Deviation: 0.8281

For the "Sepal Width" column:

    Mean: 3.054
    Minimum: 2.0
    Maximum: 4.4
    Standard Deviation: 0.4336

For the "Petal Length" column:

    Mean: 3.7587
    Minimum: 1.0
    Maximum: 6.9
    Standard Deviation: 1.7644

For the "Petal Width" column:

    Mean: 1.1987
    Minimum: 0.1
    Maximum: 2.5
    Standard Deviation: 0.7632
```

As expected, much of the math is just wrong. It appears to be returning results consistent with the standard iris dataset, implying that it isn't actually doing the math and would struggle to compute on non-standard data.

