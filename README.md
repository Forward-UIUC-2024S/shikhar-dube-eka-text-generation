# Enhanced Long-Form Text Generation Using IRP Framework

## Overview

This module enhances the IRP (Imitate, Retrieve, Paraphrase) framework to generate long-form text that adheres to various styles without explicit style specifications. It leverages techniques of plan extraction and refinement from the EIPE-Text paper to improve consistency and adherence to the desired content structure for long form content. The module's capability to adapt to any style, evidenced by its performance on datasets like Medline and Wikipedia articles, makes it a versatile tool in automated content generation.

## Setup

To set up and run this module, follow these steps. Note that the setup requires Python 3.10.12. 

1. **Python Environment**:
   Ensure you have Python 3.10.12 installed. Using a virtual environment is recommended:
   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows use `venv\Scripts\activate`
    ```
2. **Install Dependencies**:
    Install all required Python libraries from the requirements.txt file:
    ```
    pip install -r requirements.txt
    ```

3. **Structure Breakdown**:
```
shikhar-dube-ekatextgeneration/
    - requirements.txt
    - data/ 
        -- medline_data.csv
        -- wiki_paraphraser_formatted_data.pt
        -- similar_sentence_embeddings_paraphraser.json
        -- guitarist_wiki_articles.json
    - LongFormIRP.ipynb
    - readme.md
```
Important Files:
* `data/medline_data.csv`: contains all the scraped and processed data from medline 
* `data/guitarist_wiki_articles.json`: scraped guitarist bio articles from wikipedia
* `data/similar_sentence_embeddings_paraphraser.json`: saved raw embeddings - takes 1-2 hours to build from scratch 
* `data/wiki_paraphraser_formatted_data.pt`: saved formatted embeddings - takes 1-2 hours to build from scratch
* `LongFormIRP.ipynb`: contains source code and function needed to generate long from content 


## Functional Design (Usage)
This section outlines the main functions available in this module. Each function is designed for users who wish to apply the module to generate and refine text plans and content effectively.

### Plan Sketching
Generates a tree-like text outline based on a given article's style.
- **Input**: A string (`style_article_text`) containing the text from which the style will be analyzed.
- **Output**: A string containing the generated plan in a structured format.
```python
def plan_sketching(style_article_text: str) -> str
```

### QA Pairs Generation
Creates multiple-choice questions to help evaluate and refine the text plan.
- **Input**: A string (article_text) of the article for generating questions.
- **Output**: A string containing multiple-choice questions and answers based on the article content.
```python
def qa_pairs_generation(article_text: str) -> str
```

### Evaluate Plan on QA Pairs
Evaluates a given plan using generated QA pairs and provides necessary refinement instructions.
- **Input**: 
    - plan (str): The current plan for the article.
    - qa_pairs (str): QA pairs used for evaluating the plan.
- **Output**:
    - Boolean indicating whether modifications are necessary.
    - String containing detailed refinement instructions.
```python
def evaluate_plan_on_qapairs(plan: str, qa_pairs: str) -> (bool, str)
```

### Plan Refinement
Applies refinement instructions to the plan based on evaluation.
- **Input**: 
    - plan (str): The current text plan.
    - refinement_instructions (str): Instructions on how to refine the plan.
- **Output**:  A string containing the updated plan.
```python
def plan_refinement(plan: str, refinement_instructions: str) -> str
```

### Refine Plan Loop
Iteratively refines a plan by generating QA pairs and applying modifications until no further modifications are needed.
- **Input**: Initial_article_text (str): The raw cleaned text of the article from dataset
- **Output**: A string representing the final refined plan.
```python
def refine_plan_loop(initial_article_text: str) -> str
```

### Generate Plan
Generates a new plan based on a given topic using example plans from a refined plan corpus.
- **Input**:
    - topic (str): The topic for the new plan.
    - example_plans (str): Example plans for style reference.
- **Output**: A string containing the newly generated plan.
```python
def generate_plan(topic: str, example_plans: str) -> str
```

### Create Passage
Generates a detailed article based on the provided refined plan, ensuring the article reflects the embedded style and structure of the plan.
- **Input**: plan (str): The refined plan to base the article on.
- **Output**: the generated article.
```python
def create_passage(plan: str) -> str
```

## Demo Video:
Link: (https://drive.google.com/file/d/1i8iHPnQFwqzASPy16z-rWu2cRzklMCfd/view?usp=share_link)


## Algorithmic Design

This module is designed to generate and refine text plans for long-form content creation using a series of interconnected components powered by OpenAI's GPT-4 Turbo. The process begins with the `plan_sketching` function, which utilizes GPT-4 to analyze the style and thematic flow of a provided article to create a structured text plan in a tree-like format. Following this, the `qa_pairs_generation` function automatically generates multiple-choice questions that assess the accuracy and completeness of the text plan, leveraging the advanced natural language processing capabilities of GPT-4 to ensure the questions are reflective of the plan's content and structure.

The generated plans are then evaluated using the `evaluate_plan_on_qapairs` function, which compares the plans against the QA pairs to identify discrepancies or areas for improvement and outputs either add, modify or delete insctructions. Based on this evaluation the `plan_refinement` function applies these instructions to modify the plan accordingly. This iterative process of generating QA pairs, evaluating, and refining the plans is looped through until the plan meets predefined quality thresholds or no longer requires modifications.

Finally, the refined plan is stored in a corpus with refined plans. Then in the `generate_plan` and function, the refined plans are fed into the GPT-4 prompt as examples of good content plans and the LLM is tasked with leveging these plans to create a new plan on a **novel** topic of similar style. Finally, in the `create_passage` function, we use the generated plan for the novel topic to produce the full text. This comprehensive use of GPT-4 and prompting techniques ensures a robust system capable of producing high-quality, stylistically consistent long-form texts based on refined content plans.

## Issues and Future Work:

### Issues: 
- High API costs due to refinement loop taking > 5 epochs to converge on average 
- Generation sticking to narrative tone rather than absorbing tone from inital text 

### Future Imporvement Paths:
Building upon the current framework, there are several areas that could be explored to further enhance its effectiveness and applicability:

1. **Entity Selection in Plan Generation:** Investigating and implementing methods to select similar entities more effectively when generating plans could lead to improvements in content relevance and accuracy. Techniques such as semantic similarity measures or entity recognition tools could be integrated to refine the selection process.
2. **Cost Efficiency in Plan Refinement**: Exploring ways to make the plan refinement process more cost-efficient would be beneficial, especially considering the computational resources required for training and running the models are high and the training takes on average 5 epochs to converge. 
3. **Generalization to Other Text Styles:** Extending the testing of the framework to other styles of text, such as news articles, biographies, and social media posts (e.g., tweets), would be crucial for assessing its versatility and robustness. 
4. **Training Imitator Over Full corpus:** Once training is more cost efficient, train the plan extraction loop on the full dataset to create a full corpus of plans that can be used as examples when generating novel content plans for novel topics. This would drastically improve the quality of the output. 
5. **In Context Learning:** Use in context learning strategies to feed in plans that are most similar to the output and add in more plans from corpus as necessary. This has the potential to be more cost effective than simply adding in all the plans in prompt at once.

## Change log
Use this section to list the _major_ changes made to the module if this is not the first iteration of the module. Include an entry for each semester and name of person working on the module. 

Spring 2024 (Shikhar Dube)
* Week of 05/26/2024: finalized and pushed all code 

## References 
- [`EIPE - Evaluation-Guided Iterative Plan Extraction for Long-Form Narrative Text Generation`](https://arxiv.org/pdf/2310.08185.pdf)
- [`Imitate, Retrieve, Paraphrase`](https://arxiv.org/pdf/2305.03276)
- [`Instruct-SCTG: Guiding Sequential Controlled Text Generation through Instructions`](https://arxiv.org/pdf/2312.12299.pdf) 
- [`Medline`](https://medlineplus.gov)
- [`Wikipedia Articles Glossary`](https://en.wikipedia.org/wiki/List_of_guitarists#top)
- [`Wikipedia API`](https://pypi.org/project/Wikipedia-API/)

