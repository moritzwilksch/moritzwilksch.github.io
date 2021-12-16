---
layout: page
title: Portfolio
permalink: /portfolio/
---

### Research Project
**The Predictive Power of Social Sentiment Regarding Short-Term Stock Returns** ([GitHub](https://github.com/moritzwilksch/SocialMediaBusinessAnalytics) | [Research Report](https://github.com/moritzwilksch/SocialMediaBusinessAnalytics/raw/main/SMRP_Report_Submission.pdf))
- Collected >5M tweets mentioning the 10 most discussed stocks on Twitter
- Trained a custom sentiment analysis model on TF-IDF representation of manually annotated subset to extract domain-specific financial sentiment
- Used sentiment and traditional technical indicators to forecast next-day stock return using LightGBM and Random Forests
- compared performance to pre-trained off-the-shelf sentiment model

### Deep Learning
**Rossmann Sales Prediction** ([GitHub](https://github.com/moritzwilksch/RossmannSalesPrediction))
- Used neural network with trained entity embeddings to forecast sales for ~1,000 Rossmann stores
- solved problems induced by hierarchical and time-series nature of the data set
- compared performance against traditional tabular learners

**Yelp Review Classification** ([GitHub](https://github.com/moritzwilksch/DL_Project_Yelp))
- Used Convolutional Neural Networks for text classifications of Yelp reviews
- Use fasttext word embeddings for token representation

### Cloud-Based
**AWS Cloud-Native Text Generator, Team Project** ([GitHub](https://github.com/dai-anna/AWSCloud-TweetGenerator))
- Built end-to-end cloud-based pipeline to collect and clean Tweets and (re-)train and deploy machine learning model
- Pipeline is orchestrated to run daily and is deployed as a web application as well as a Twitter bot
- Used AWS Lambda, S3, EC2, ECR, EventBridge, and Batch as well as IaC (python SDK) and Docker follwing CI best practices using GitHub Actions

**Durham News Analysis on AWS** ([GitHub](https://github.com/moritzwilksch/AWS-DurhamNewsAnalysis))
- Built webscraper that runs locally or in AWS Lambda to scrape a local news website for Durham, NC
- Used AWS Comprehend for NLP of news stories
- Conneted to AWS DynamoDB for storing historic data


### Freelancing
**Descriptive Business Analytics** (Not public)
- Analyzed multiple years of transaction data from a small local restaurant's cash register
- Prepared analytic reports informing future choice of menu and staffing schedule to reduce complexity and cost