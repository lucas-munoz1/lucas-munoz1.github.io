# Table of Contents
- [About](#about)
- [Ecommerce Product Analysis Engine](#engine)
  - [Summary](#summary1)
  - [Data & Database Overview](#dataoverview1)
  - [Data Scraping](#scraping)
  - [Modeling](#modeling)
- [KIPP Texas Data Analysis](#kipp)
- [Kaggle Housing Data Analysis](#kaggle)

# About {#about}
This portfolio showcases three projects completed during my undergraduate studies. The first project, the Ecommerce Analysis Engine, was started in November 2023 as an effort to incorporate data-driven 
decisions into a potential ecommerce business. To preserve the confidentiality and integrity of the  processes involved, I have included figures depicting the processes involved in lieu of directly 
uploading the code.

The second project, KIPP Texas Data Analysis, was undertaken as part of a consulting class in the Spring of 2024. My team and I were tasked with analyzing a substantial dataset provided by KIPP Texas, 
comprising over 200,000 entries, to identify demographic factors influencing test score outcomes. Similarly, to ensure the confidentiality of the dataset, I have included screenshots of the code and 
analysis instead of the actual files.

Lastly, the Kaggle Housing data analysis project was completed in March of 2023 as part of a machine learning class. I was given a large housing dataset, and tasked with cleaning and modeling to predict 
housing prices based on physical attributes of a house. As this dataset is publicly available, I have attached both the dataset, my code files, and a link to the data source to this page. 

# Ecommerce Product Analysis Engine {#engine}
## Summary {#summary1}
The primary objective of this project was to develop an engine capable of identifying market gaps and deriving specific insights into the product attributes driving the sales of various e-commerce 
products. This included the development of effective scraping code, a robust database, and three machine learning models  - sentiment analysis and named entity recognition with SpaCy, and text 
classification using XGBoost and BERT. The following sections will discuss each component in-depth. 

## Data & Database Overview {#dataoverview1}
The data was sourced from three ecommerce websites: Amazon, AliExpress, and Alibaba. The database was configured to store information regarding each product (title, description, price, etc.), and their 
customer reviews (relationship to a product, review text, subtitle, etc.). Furthermore, through the use of lookup tables, the database stores product relationships to each other, based on Amazon, 
AliExpress, and Alibaba’s given product relationships (products that appear under ‘Frequently bought together’, or ‘Related Items’). 

_Figure 1: Final Draft Relational Schema_
![Analytics - Final Relationship Schema](https://github.com/lucas-munoz1/lucas-munoz1.github.io/assets/170210558/32a25523-1e6f-4932-9198-0791c90ad6b9)

The relational schema shown in figure 1 depicts each entity in the database, as well as how the entities are connected. 

After creating the relational schema shown in figure 1, I used DBeaver to create and manage a local PostgreSQL database. Populating the database involved mass data scraping. To connect the scraping 
scripts to the database, I wrote a series of functions that used the Psycopg2 library and SQL to upload data to the right tables, as shown in figure 2. Data scraping will be discussed in depth in the next 
section.

VISUAL PLACEHOLDER: DATABASE PSYCOPG2 FUNCTIONS

Finally, figures 3 and 4 show the first five rows of both the product and customer review tables. 

_Figure 2: First Five Products in Database_
![db product query](https://github.com/lucas-munoz1/lucas-munoz1.github.io/assets/170210558/5f162dee-513e-4797-87ba-3cda8781cfcd)

_Figure 3: First Five Customer Reviews in Database_
![db custreview query](https://github.com/lucas-munoz1/lucas-munoz1.github.io/assets/170210558/ff57822b-33b4-40d0-ae81-f3298f0fb1f1)

## Data Scraping {#scraping}
In order to get product data to use for both analysis and model development, I developed a series of web scrapers using Selenium 4.18.1 and beautifulsoup4 4.12.3. I began by creating template scraping 
classes for single product pages on Amazon, AliExpress, and Alibaba, as well as a class to collect customer review data from Amazon. 
The data collection process was divided into two sections:

1. Collect data to make machine learning models

   First, I needed to collect data specifically for modeling. In order to make a model that generalizes well to data it hasn’t been trained on, I needed a rich variety of products from many different 
   categories. To address this, I created two scrapers for Amazon and Aliexpress, that navigated through the sites given categories, and collected product links. These links, fed into the scraping classes 
   for single product pages, allowed me to collect my first dataset, which was used throughout the modeling process.

   VISUAL PLACEHOLDER: GIF OF SCRAPING IN ACTION - ALIEXPRESS
   
   Figure X shows the link collection script in action, navigating through each category, and storing the product links that it finds. 

3. Collect data to analyze
   
   After modeling was completed, I needed a way to collect data specific to products I was interested in analyzing. To address this, I created a scraper to search on Amazon for manually entered product 
   names and collect an arbitrary number of products and customer reviews. This script allowed me to collect new datasets as needed.
   
   VISUAL PLACEHOLDER: GIF OF SCRAPING IN ACTION – AMAZON
   
   Figure X depicts the Amazon data collection script in action, navigating to the search results of a given product, collecting and storing product data.


VISUAL PLACEHOLDER: FLOWCHART OF DATA COLLECTION 

Figure X visualizes the movement of the data collected….. blah blah blah

## Modeling {#modeling}
Following the creation and initial population of the database, it came time to develop machine learning models that would shape the analysis portion of this engine. 

### NER with SpaCy
The first model I created was a Named Entity Recognition model using SpaCy, designed to extract the ‘type’ of a product from its title. A product’s ‘type’ refers to its most fundamental category. For 
example, if a product title is ‘Mechanical feeling gaming keyboard’, the type would be ‘keyboard’. This model aimed to categorize products effectively for further analysis and comparison. 

#### Data Collection & Annotation
To create this model, I extracted a substantial dataset of product titles from the database. Initially, this dataset contained 630 documents, which I expanded to 3245 documents through extensive 
annotation. I used Doccano, an open-source annotation tool, to label the data. The annotation process involved: 

  1.	Defining ‘Type’: Clearly defining the ‘type’ and establishing annotation rules to ensure consistency
  2.	Formatting data: Converting the raw JSON data from the database into JSON Lines format compatible with Doccano 
  3.	Manual Annotation: Manually labeling the product titles according to the predefined rules.

VISUAL PLACEHOLDER: FLOWCHART OF DATA COLLECTION & ANNOTATION PROCESS

#### Model Training & Evaluation
After annotating approximately half of the initial dataset, I began training the NER model to identify areas for improvement. From this point on, the steps included: 

1.	Configuring the model: Downloading an NER configuration file from the SpaCy website and converting the annotated JSON Lines data into SpaCy’s required format. This involved:

  a.  Converting texts into ‘docs’ and indexing each annotated label
  b.	Organizing all docs into a DocBin
  c.	Splitting the dataset into training and test sets.

  VISUAL PLACEHOLDER: DIAGRAM SHOWING CONVERSION FROM LINES TO SPACY

2.	Training the model: Using SpaCy to train the NER model on the annotated dataset, iteratively improving the model by refining annotations and retraining.

  VISUAL PLACEHOLDER: SC OF TRAINING PROCESS
  
3.	Evaluation metrics: Assessing the model’s performance using precision, recall, and F-score. The final model achieved the following metrics:

  a.	Precision: 0.895 
  b.	Recall: 0.917
  c.	F-score: 0.906

  VISUAL PLACEHOLDER: TABLE DISPLAYING CHANGES IN PERFORMANCE OVER TIME 
  
#### Challenges & Solutions
A significant challenge during the annotation process was maintaining consistency across a growing dataset. To address this, I: 

  1.	Documentation: Kept a detailed record of labeling rules and examples to ensure consistent annotations. 
  2.	Auto labeling: Implemented auto labeling by hosting the latest version of the model on a Flask web server and connecting it to Doccano. This allowed me to assess the model’s performance in real- 
      time, providing valuable insights for further improvements and speeding up the annotation process.

VISUAL PLACEHOLDER: AUTO LABELING GIF SPLIT – ONE SIDE SHOWING LABELING – OTHER SHOWING TERMINAL OUTPUT 

#### Conclusion 
My goal was to develop a model with an F-score above 0.9. Through iterative training and refinement, I successfully created an NER model that met this benchmark, achieving an F-score of 0.906. This model 
now serves as a foundational component for organizing and analyzing product titles.

### Sentiment Analysis with SpaCy 
The second model I developed was a sentiment analysis model using SpaCy's textcat component to classify customer review subtitles as positive, neutral, or negative. I opted to use review subtitles instead 
of full reviews because the subtitles more directly conveyed customers' emotions, whereas the full reviews often contained more detailed content. This model served two primary purposes:

1.	Comparing Sentiment Distributions: To compare sentiment distributions between different products.
2.	Analyzing Customer Reactions: To isolate and analyze customer reviews based on their sentiment, facilitating targeted analysis of product attributes that elicit negative reactions.
   
#### Data Collection and Preprocessing
I began by extracting a dataset of 600 review subtitles from Amazon. Learning from previous experience, I ensured to preprocess the data before annotation to avoid issues such as handling emojis, blank 
texts, and non-English texts. I implemented data cleaning functions in Python to ensure a clean dataset for annotation.

VISUAL PLACEHOLDER: FLOWCHART OF DATA COLLECT AND PREPROCESSING STEPS

#### Annotation Guidelines
I established a set of rules to ensure consistent annotation:

1.  Positive: Subtitles indicating benefit, excitement, happiness, or any other positive emotions.
2.  Neutral: Subtitles that have both positive and negative sentiments (e.g., "good size, but poor quality"), subtitles without a clear sentiment, or one-word adjectives.
3.  Negative: Subtitles indicating a negative experience, something wrong, missing, or needed for the product.

VISUAL PLACEHOLDER: EXAMPLE OF ANNOTATED SUBTITLES SHOWING POSITIVE, NEUTRAL, NEGATIVE

#### Addressing Imbalanced Data
During annotation, I observed a significant imbalance with a majority of positive subtitles. To correct this, I utilized Amazon's star ratings from the database. I pulled an additional 1000 subtitles with 
ratings of 3 stars or lower to balance the distribution effectively, resulting in a final annotated dataset of 3500 documents.

VISUAL PLACEHOLDER: BAR OR PIE CHART SHOWING DISTRIBUTION OF P,N,N BEFORE AND AFTER BALANCING

#### Handling Diverse Language
A challenge I encountered was the diversity of language, particularly adjectives used consistently in one context, which confused the model. To address this, I generated synthetic data using ChatGPT. This 
involved creating synthetic subtitles using problematic words in positive, neutral, and negative contexts. This approach improved the model's performance.

VISUAL PLACEHOLDER: EXAMPLE OF SYNTHETIC DATA GENERATED USING CHATGPT

#### Model Training and Performance
I trained the sentiment analysis model using SpaCy's textcat component. The final model achieved the following performance metrics:

- Positive:
    Precision: 0.890
    Recall: 0.886
    F-score: 0.888
  
- Neutral:
    Precision: 0.859
    Recall: 0.822
    F-score: 0.840
  
- Negative:
    Precision: 0.867
    Recall: 0.904
    F-score: 0.885
  
VISUAL PLACEHOLDER: TABLE SHOWING CHANGING METRIC SCORES 

While the model's performance metrics were slightly below my initial goal of 0.9, I was satisfied with the results and decided to proceed to the next model, knowing I could return to improve it later.

### Text Classification with BERT 
The final model I developed for this engine was a text classification model designed to identify market gap language in customer reviews. Market gap language is defined as language describing a customer's 
desire for an attribute that was not included in the product. While I won't delve into the specific rules and annotation process to maintain confidentiality, I will detail the development and training of 
this model.

This model was built using XGBoost in combination with several embedding and analysis techniques:
  - Continuous Bag of Words (CBOW) model using Word2Vec
  - Sentiment analysis with TextBlob
  - POS & DEP tags
  - BERT embeddings from a fine-tuned, pretrained BERT model
  
#### Continuous Bag of Words (CBOW) Model
For the CBOW model, I trained a Word2Vec model on a dataset of 100,000 customer reviews extracted from the database. This process involved the following steps: 

1.	Data Preparation: Tokenizing and preprocessing the reviews to ensure they were in a suitable format for training.
2.  Model Training: Training the Word2Vec model with the following parameters:
    - vector_size=100: The dimensionality of the word vectors.
    - window=5: The maximum distance between the current and predicted word within a sentence.
    - min_count=1: Minimum frequency count of words to be included in the training.
    - sg=0: Specifying the use of the CBOW model.
    
This Word2Vec model provided word-level embeddings that captured the context and semantic relationships between words in the reviews.

VISUAL PLACEHOLDER: DIAGRAM SHOWING DATA PREP & WORD2VEC TRAINING PROCESS

#### BERT Embeddings
For BERT embeddings, I fine-tuned a pretrained BERT model on the same dataset of 100,000 customer reviews. The process included:

1.	Fine-Tuning BERT:	Using transfer learning to adapt the pretrained BERT model to the specific domain of customer reviews. This involved:
  - Training the model on the review dataset to adjust the weights and improve its understanding of the specific language and context used in the reviews.
  - Implementing techniques such as learning rate scheduling and dropout to prevent overfitting and ensure robust performance.
2.  Extracting Embeddings: After fine-tuning, I extracted the embeddings from the BERT model. These embeddings provided deep contextual representations of the review texts, capturing nuanced meanings and 
    relationships between words and phrases.

VISUAL PLACEHOLDER: DIAGRAM SHOWING FINE-TUNING & EMBEDDING EXTRACT PROCESS 

#### Model Integration and Training
The final text classification model combined the embeddings and features from the various components:

 - Word2Vec Embeddings: Provided efficient word-level context and semantic relationships.
 - BERT Embeddings: Captured deep contextual understanding and nuances of the text at the sentence and document level.
 - Sentiment Analysis: Used TextBlob to analyze the sentiment of each review, adding an additional layer of understanding by quantifying the emotional tone.
 - POS & DEP Tags: Part-of-speech and dependency tags were extracted to include syntactic information.

These features were fed into an XGBoost model for training. The combined approach allowed the model to leverage the strengths of different embedding techniques and feature analyses, resulting in a more 
comprehensive understanding of the text.

VISUAL PLACEHOLDER: DIAGRAM SHOWING INTEGRATION OF FEATURES INTO XGBOOST

#### Model Performance and Future Work
The final model achieved an accuracy of 0.85 on a test set of 800 annotated rows. While this is a promising result, the model is still a work in progress. Future improvements will focus on:

  - Increasing the size and diversity of the annotated dataset to improve the model's generalizability.
  - Fine-tuning the hyperparameters of the XGBoost model to further enhance performance.
  - Exploring additional features and techniques to capture more complex language patterns and relationships.

This model represents a significant step forward in identifying market gap language in customer reviews, providing valuable insights for product development and customer satisfaction analysis.

VISUAL PLACEHOLDER: TABLE SHOWING CHANGING METRICS UNTIL FINAL

# KIPP Texas Student Data Analysis {#kipp}

# Kaggle Housing Data Analysis {#kaggle}
