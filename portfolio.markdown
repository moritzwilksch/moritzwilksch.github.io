---
layout: page
title: Portfolio
permalink: /portfolio/
---

### Research Projects
**[Master Thesis, WIP] pyFin-Sentiment: Sentiment Analysis of Financial Social Media Posts** ([GitHub](https://github.com/moritzwilksch/MasterThesis))
- collected and annotated data set of 10,000 tweets to build a market sentiment analysis (SA) model that works on social media posts discussing investment decisions
- trained and benchmarked linear models, SVM, recurrent neural nets, transformer-based neural nets, and a BERT-based LLM against SoTA from the domain of finance-related SA and generic social media SA
- deployed best model as an easy-to-use python libary. Check it out: [pyFin-Sentiment repo](https://github.com/moritzwilksch/pyfin-sentiment), [pyFin-Sentiment Docs](https://pyfin-sentiment.readthedocs.io/en/latest/)

**The Predictive Power of Social Sentiment Regarding Short-Term Stock Returns** ([GitHub](https://github.com/moritzwilksch/SocialMediaBusinessAnalytics) | [Research Report](https://github.com/moritzwilksch/SocialMediaBusinessAnalytics/raw/main/SMRP_Report_Submission.pdf))  
*Published at the student track at Internationale Tagung Wirtschaftsinformatik ([WI22](https://wi22.de)). Download the paper [here](https://aisel.aisnet.org/wi2022/student_track/student_track/38/)*
- Collected >5M tweets mentioning the 10 most discussed stocks on Twitter
- Trained a custom sentiment analysis model on TF-IDF representation of manually annotated subset to extract domain-specific financial sentiment
- Used sentiment and traditional technical indicators to forecast next-day stock return using LightGBM and Random Forests
- compared performance to pre-trained off-the-shelf sentiment model

---

### Deep Learning
**Rossmann Sales Prediction** ([GitHub](https://github.com/moritzwilksch/RossmannSalesPrediction))
- Used neural network with trained entity embeddings to forecast sales for ~1,000 Rossmann stores
- solved problems induced by hierarchical and time-series nature of the data set
- compared performance against traditional tabular learners to assess viability of tabular deep learning

**Deep Document Classification, Team Project** ([GitHub](https://github.com/dai-anna/Duke-NLP-FinalProject))
- Benchmarked LDA and Bi-directional GRU neural network with self-trained word embeddings on classifying documents into topics
- Used Twitter API to collect tweets for seven distinct topics
- Let LDA infer latent document-topic and word-topic distributions and used these for classification
- Assessed the learned semantics of the word embedding vectors in the neural network

**Text Generation: Star Trek Scripts** ([GitHub](https://github.com/moritzwilksch/StarTrekWriter) | [Blog Post](https://moritzwilksch.github.io/2022/01/14/star-trek-text-generation.html))
- Used Recurrent Neural Network (GRU) for generating new *Star Trek: The Next Generation* scripts
- Next-token prediction on corpus of scripts tokenized with BERT tokenizer

---

### Cloud-Based
**AWS Cloud-Native Text Generator, Team Project** ([GitHub](https://github.com/dai-anna/AWSCloud-TweetGenerator))
- Built end-to-end cloud-based data pipeline to collect and clean Tweets and (re-)train and deploy machine learning model for generating tweets for currently trending hashtags
- Pipeline is orchestrated to run daily and is deployed as a web application as well as a Twitter bot
- Used AWS Lambda, S3, EC2, ECR, EventBridge, and Batch as well as IaC (python CDK) and Docker following CI best practices using GitHub Actions

**Durham News Analysis on AWS** ([GitHub](https://github.com/moritzwilksch/AWS-DurhamNewsAnalysis))
- Built webscraper that runs locally or in AWS Lambda to scrape a local news website for Durham, NC
- Used AWS Comprehend for NLP of news stories
- Conneted to AWS DynamoDB for storing historic data

---

### Statistical Modeling
**Prices in the Berlin Real Estate Market** ([GitHub](https://github.com/moritzwilksch/BerlinRealEstatePrices))
- Scraped data on major online real estate websites over 6 months
- Used linear regresion and a hierarchical random-slopes model to model the relationship between apartment features and prices in R
- Visualized random slopes on a map using GeoJSON data and `geopandas`

**Duke Datathon 2021 (Winning Entry)**
- analyzed longitudinal survey data to understand socio-economic situation of citizens across 18 Asian countries 
- conducted extensive data cleaning to structure hundreds of text-based survey items
- utilized factor analysis to understand how demographic characteristics relate to three latent factors, e.g. "support of a totalitarian system"

---

### Freelancing
**Descriptive Business Analytics** (Not public)
- Analyzed multiple years of transaction data from a small local restaurant's cash register
- Prepared analytic reports informing future choice of menu and staffing schedule to reduce complexity and cost
