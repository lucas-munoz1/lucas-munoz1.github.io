# About
Lucas Munoz 

# Projects 

## Ecommerce Product Analysis Engine 
### Summary
  The primary objective of this project was to develop an engine capable of identifying market gaps and deriving specific insights into the factors driving the sales of various e-commerce products. This required the creation of effective scraping code, a robust database, two named entity recognition models - one to determine a product's type and one to determine a product's attributes, and a sentiment analysis model on customer review subtitles. The following sections will outline the data overview, creation and deployment of the database, the scraping code and methods, and finally the development and current status of the machine learning models. 

### Data & Database Overview
  The data was sourced from three ecommerce websites: Amazon, AliExpress, and Alibaba. The target data includes each products title, description, link, price, rating, type, keywords, website name, lead times, historical price, related searches, and category. Furthermore, each product had customer reviews, and for those reviews, the target data includes the review rating, subtitle, text, style, location, date, it's 'helpful' variable (how many people found the review helpful), and the review site. 

_Figure 1: Final Draft Entity Relationship Diagram_
![image (7)](https://github.com/lucas-munoz1/lucas-munoz1.github.io/assets/170210558/8a86e5bb-2641-41d9-9eb0-85386072ddfb)
This diagram accounts for all of the data listed above, with some additions. On each site, there were certain types of related products. For example, on Amazon and Alibaba, you often see 'Frequently bought together'. These relationships are recorded in the database through product relationships with themselves ('hasRelated' and 'hasAssigned'). 

_Figure 2: Final Draft Relational Schema_
![Analytics - Final Relationship Schema](https://github.com/lucas-munoz1/lucas-munoz1.github.io/assets/170210558/32a25523-1e6f-4932-9198-0791c90ad6b9)
The relational schema shown in figure 2 depicts each relationship in a format that will translate to a database. 

Lastly, I chose to use a local Postgresql database, and managed it using Dbeaver. The following screenshots show the first five rows of products and customer reviews. 

_Figure 3: First Five Products in Database_
![db product query](https://github.com/lucas-munoz1/lucas-munoz1.github.io/assets/170210558/5f162dee-513e-4797-87ba-3cda8781cfcd)

_Figure 4: First Five Customer Reviews in Database_
![db custreview query](https://github.com/lucas-munoz1/lucas-munoz1.github.io/assets/170210558/ff57822b-33b4-40d0-ae81-f3298f0fb1f1)

### Data Scraping
  In order to get data to use for analysis, I created webscrapers using Selenium 4.18.1 and beautifulsoup4 4.12.3. This process involved creating template scraping classes for Amazon, Alibaba, and AliExpress products, and using those templates in various methods to target specific types of data (Figure 5). However, these classes needed a product link to navigate to, so I also created a script which navigated through the various categories on the three websites to collect product links (Figure 6). Finally, I needed a way to transfer this data to the database, so I made a list of importable functions which handled all compleities of uploading data to the database. (Figure 7)

_Figure 5: Example Code for Alibaba Product Details_
![scraping product details](https://github.com/lucas-munoz1/lucas-munoz1.github.io/assets/170210558/5ed2625e-8c4b-4b5e-9424-a3672810a86c)

_Figure 6: Example Code to Collect Product Links_
![scrape product links](https://github.com/lucas-munoz1/lucas-munoz1.github.io/assets/170210558/a032f874-97f8-46bc-89e1-9d0b379dae28)

_Figure 7: Example Code Upload to Database_
![db upload](https://github.com/lucas-munoz1/lucas-munoz1.github.io/assets/170210558/c6b5229a-52fd-466f-a3b8-1cdb8fd6ab82)

## Project 2

## Project 3
