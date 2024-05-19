# About
Lucas Munoz 

# Projects 

## Ecommerce Product Analysis Engine 
### Summary
  The primary objective of this project was to develop an engine capable of scraping data, segmenting it into manageable and organizable portions, and analyzing it to identify market gaps and derive specific insights into the factors driving the sales of various e-commerce products. This required the creation of effective scraping code, a robust database, two named entity recognition models - one to determine a product's type and one to determine a product's attributes, and a sentiment analysis model on customer review subtitles. 
The current structure allows for additional models to be created in the future, including a product category classification model, a product relationship classification model, and an aspect based sentiment analysis model to extract key information from customer reviews. These future plans are reflected in the creation of the database and the type of data that has been collected.
The following sections will outline the data overview, creation and deployment of the database, the scraping code and methods, and finally the creation and current status of the machine learning models. 

### Data & Database Overview
  The data was sourced from three ecommerce websites: Amazon, AliExpress, and Alibaba. The target data includes each products title, description, link, price (original price and price on sale), rating, type, keywords, and website name. Furthermore, each product had customer reviews, and for those reviews, the target data includes the review rating, subtitle, text, style, location, data, it's 'helpful' variable, and the review site. 

The database was created to reflect the target data. The final draft of the entity-relationship diagram is shown below: 
_Figure 1: 5th Draft ERD Ecommerce Product Engine Database_
![5th Draft ERD](https://github.com/lucas-munoz1/lucas-munoz1.github.io/assets/170210558/1a264618-0bf4-4169-b5d2-be6ec9d53005)
This diagram accounts for all of the data listed above, with some additions. On each site, there were certain types of related products. For example, on Amazon and Alibaba, you often see 'Frequently bought together'. These relationships are recorded in the database through an 'isRelatedTo' attribute for each product. Additionally, there is a 'historicalPrice' attribute for each product. This data comes from a source outside of the three main websites. 
Finally, there are 'userProject' and 'user' entities. These entities reflect the initial setup for creating a user interface for this engine, but they are currently not in use. 



## Project 2

## Project 3
