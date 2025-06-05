---
layout: default
title: Ecommerce Product Analysis Engine
---
# Table of Contents
  - [Summary](#summary1)
  - [Data & Database Overview](#dataoverview1)
  - [Data Scraping](#scraping)
  - [Named Entity Recognition](#NER)

<a href="/" class="button">Back to Home</a>

# Ecommerce Product Analysis Engine {#engine}
## Summary {#summary1}
The primary objective of this project was to develop an engine capable of identifying market gaps and deriving specific insights into the product attributes driving the sales of various e-commerce products. This included the development of effective scraping code, a robust database, and several machine learning models. 

The database formed the foundation for the engine, and with a growing 25,000 product data points and over 800,000 customer reviews, it facilitates the development of machine learning models, as well as fast and large-scale analyses. Furthermore, there are three primary models used to analyze data, a Named Entity Recognition model, a Sentiment Analysis model, and a Text Classification model. In this section of my portfolio, I will discuss how and why I developed the database, the scraping code, and the Named Entity Recognition model.

## Data & Database Overview {#dataoverview1}
The data was sourced from three ecommerce websites: Amazon, AliExpress, and Alibaba. The database was configured to store information regarding each product (title, description, price, etc.), and their 
customer reviews (relationship to a product, review text, subtitle, etc.). Additionally, through the use of lookup tables, the database stores product relationships to each other, based on Amazon, 
AliExpress, and Alibaba’s given product relationships (products that appear under ‘Frequently bought together’, or ‘Related Items’). 

_Figure 1: First Draft Relational Schema_
![Analytics - Final Relationship Schema](https://github.com/lucas-munoz1/lucas-munoz1.github.io/assets/170210558/32a25523-1e6f-4932-9198-0791c90ad6b9)

The relational schema shown in figure 1 depicts each entity in the database, as well as how the entities are connected. I planned to work with thousands of products and customer reviews throughout this project, so starting with proper organization of the data and its relationships helped me avoid slow query times, unexpected behavior, and unorganized or incorrect query outputs.

After creating the relational schema shown in figure 1, I used DBeaver to create and manage a local PostgreSQL database. Populating the database involved mass data scraping. To connect the scraping 
scripts to the database, I wrote a series of functions that used the Psycopg2 library and SQL to upload data to the right tables, as shown in figure 2.

_Figure 2: Example SQL/Pyscopg2 Functions_
![Datbase import functions but better](https://github.com/user-attachments/assets/75e82c68-79bd-4231-bf6d-3372a1b98be1)

Figure 2 shows a portion of the database upload functions used in the data collection process. Since I used Python to scrape the data, these functions moved the data from objects in the Python scraping scripts, to their correct database tables. Lastly, figures 3 and 4 show the first five rows of both the product and customer review tables. 

_Figure 3: First Five Products in Database_
![db product query](https://github.com/lucas-munoz1/lucas-munoz1.github.io/assets/170210558/5f162dee-513e-4797-87ba-3cda8781cfcd)

_Figure 4: First Five Customer Reviews in Database_
![db custreview query](https://github.com/lucas-munoz1/lucas-munoz1.github.io/assets/170210558/ff57822b-33b4-40d0-ae81-f3298f0fb1f1)

## Data Scraping {#scraping}
In order to get product data to use for both analysis and model development, I developed a series of web scrapers using Selenium 4.18.1 and beautifulsoup4 4.12.3. I began by creating template scraping 
classes for single product pages on Amazon, AliExpress, and Alibaba, as well as a class to collect customer review data from Amazon. 
The data collection process was divided into two sections:

### Data for Modeling

First, I needed to collect data specifically for modeling. In order to make a model that generalizes well to data it hasn’t been trained on, I needed a rich variety of products from many different 
categories. To address this, I created two link scraping classes for Amazon and Aliexpress, that navigated through the sites given categories, and collected product links. These links, fed into the product page scraping classes, allowed me to collect my first dataset used throughout the modeling process.


_Figure 5: AliExpress Scraping Example_
<video width="100%" controls loop="" muted = "" autoplay="">
  <source type="video/mp4" src="/media/AE_scrape_example.mp4">
</video>


Figure 5 shows the fully automated process of collecting product links from AliExpress. The code was setup to retrieve a small amount of links from a large number of different categories. This created an even distribution of products between categories, ensuring models trained on the data would not be biased to one particular category.

### Data for Analysis 
   
After modeling was completed, I needed a way to collect data specific to products I was interested in analyzing. To address this, I created a scraper to search on Amazon for manually entered product 
names and collect an arbitrary number of products and customer reviews. This script allowed me to collect new datasets as needed.


_Figure 6: Amazon Scraping Example_
<video width="100%" controls loop="" muted = "" autoplay="">
  <source type="video/mp4" src="/media/AM_scrape_example.mp4">
</video>


Figure 6 depicts the fully automated process of collecting product and customer review data from products on Amazon. The script took two input variables, a product name, and an amount of products to retrieve. The product name was used to search for the product on Amazon, as well as with the Named Entity Recognition model (discussed in the next section), which finds a product's 'type' from its title. 

For each product that was scraped, the NER model determined if that product's 'type' was equivalent to the input product name. This allowed me to filter out any product that wasn't what I was looking for, to avoid including multiple types of products in one analysis.

## Modeling {#modeling}
Following the creation and initial population of the database, it came time to develop machine learning models that would shape the analysis portion of this engine. 

### NER with SpaCy {#NER}
The first model I created was a Named Entity Recognition model using SpaCy, designed to extract the ‘type’ of a product from its title. A product’s ‘type’ refers to its most fundamental category. For example, if a product title is ‘Mechanical feeling gaming keyboard’, the type would be ‘keyboard’. 

This main purpose of this model was to be able to collect/organize datasets by their product type. Without it, I would have limited options when it came to pulling specific types of products from the database. For example, for a company that sells baby strollers, I can query all product titles with the word "stroller". This query would include products like "stroller shades", or "stroller cushions". Instead, I can query all product titles and IDs, run each title through the NER model to extract product types, and only keep the IDs which have "stroller" as their type.

#### Annotation Guidelines
To create this model, I queried a dataset of 630 product titles from the database, which I expanded to 3245 documents with iterative annotation and training. To annotate these product titles, I used Doccano (an open-source annotation tool), which I ran through a docker container on my local machine. For each title I found the products 'type' according to the following definition: 
  1. Type - The word or words that specify what the product is, within the context of a broader category; there can be multiple types in a product; the type should always be describing the product, not what the product is made up of or made from. 

#### Reformatting
Since the data queried from the database came in JSON, and Doccano only accepts 
JSON Lines, I wrote a Python script that converted JSON documents into JSON Lines. 

_Figure 8: Json to Json Lines Function_
![json to jsonl](https://github.com/user-attachments/assets/e67c4be5-c794-4a8c-8a89-703f7955a328)

Figure 8 shows the function used to convert the JSON document pulled from the database into a Json Lines format.

Similarly, after the annotation was done, I had to reformat the JSON Lines documents into SpaCy's required format. This involved:

  - Converting texts into ‘docs’ and indexing each annotated label
  - Organizing all docs into a DocBin
  - Splitting the dataset into training and test sets.
    
_Figure 9: Json Lines to Spacy DocBin Functions_
![spacy format example 2](https://github.com/user-attachments/assets/b02e93b6-76a9-4417-b77a-3cac6396c1dd)

Figure 9 depicts the functions used to convert the annotated data from Doccano into Spacy formatted data.

#### Consistency & Auto Labeling
A significant challenge I faced during the annotation process was maintaining consistency across a growing dataset. Often times I would come across a type of product and not remember how I had designated it's type previously. Furthermore, the search indexing on the version of Doccano that I needed to use was slightly broken, so I was unable to properly search through the dataset after I annotated it. To address this, I: 

  -  Kept a detailed record of labeling rules and examples to ensure consistent annotations.
  -  Implemented auto labeling by hosting the latest version of the model on a Flask web server and connecting it to Doccano. This allowed me to assess the 
     model’s performance in real- 
     time, providing valuable insights for further improvements and speeding up the annotation process.

_Figure 10: Auto Labeling with Doccano & a Flask server_
<video width="100%" controls loop="" muted = "" autoplay="">
  <source type="video/mp4" src="/media/auto-labeling.mkv">
</video>

Figure 10 is an example of using a Flask server to host the NER model, allowing Doccano to automatically label new datapoints. The Flask server accepts a product title in json format, loads the latest version of the NER model, uses the "predict" function to determine the start and end (start_offset, end_offset) of the titles "type", and returns that information to Doccano. 

Auto labeling allowed me to observe first hand how the model behaved, and correct its mistakes rather than label everything manually. Not only did this speed up the annotation process, but it gave me a better understanding of when the model needed more context.

#### Model Training & Evaluation
This model was iteratively trained using SpaCy's NER component, and after consistently refining the annotations, the final model achieved the following metrics: 
  - Precision: 0.895
  - Recall: 0.917
  - F-score: 0.906

My goal was to develop a model with an F-score above 0.9. Through iterative training and refinement, I successfully created an NER model that met this benchmark, achieving an F-score of 0.906. This model now serves as a foundational component for organizing products in the database, and ensuring consistency in the datasets throughout my analyses. 

Throughout the course of training this model, I learned valuable lessons, usually the hard way. For instance, I did not preprocess the data before annotation, which led to frustrating errors, such as SpaCy's training pipeline failing when it encountered emojis. Now I know how to remove emojis (and other preprocessing techniques), and I know to prevent them from appearing in the first place. Similarly, I refined my understanding of how NLP algorithms interpret large datasets. Specifically, when there is one word that can appear in different contexts within documents of a dataset, the NER algorithm needs multiple, consistently annotated, examples of that word in all of its varying contexts.
