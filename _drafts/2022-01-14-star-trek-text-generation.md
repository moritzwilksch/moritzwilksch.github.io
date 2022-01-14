---
layout: post
title:  "Writing Star Trek Scripts: Neural Networks for Text Generation"
date:   2022-01-14 09:45 -0500
tags: neural-network text-generation star-trek
---

With large pre-trained transformer neural networks like GPT-3 or GPT-J becoming more advanced in generating realistic looking text, I was wondering whether I could train a small, domain-specific model to generate coherent text. Here's what I learned.

## The Task
First things first: We need an intersting corpus of documents to train the model on. I want it to be domain-specific to gauge how well the model is able to pick up on domain-specific concepts, and neither be too small or too large. Inspired by a friend's suggestion I found [a dump of all Star Trek scrpits on Kaggle](https://www.kaggle.com/gjbroughton/start-trek-scripts). So let's build a network that writes *Star Trek: The Next Generation* scripts!

## Preprocessing
### Tokenization
Text generation is generally framed as a next-token prediction task, i.e. the goal is to predict the next token given all previous tokens. That raises the question: What is a token? Usually tokens are defined as single characters, sub-words, or entire words. Each approach has its pros and cons:

| Method | Advantages | Disadvantages |
| --- | --- | --- |
| **Character-based** | Can generate new words, no out-of-vocab problems, small vocabulary size | Longer training time, can generate jibberish |
| **Subword-based** | Combines advantages from word & character based | Complex vocabulary generation process |
| **Word-based** | Shorter sequence lengths | Out-of-vocabulary problem, cannot generate new words, large vocab size |

Given my limited corpus size and available compute power, I decided to go with the sub-word tokenization approach using a BERT tokenizer. Given a fixed vocab size it combines single characters into subwords for the most frequently occuring patterns in a text. Given that for the Star Trek corpus the character-based tokenization would've had a voab size of around 90 and the word-based method would've had a vocab size of around 15,000, I decided to set the vocabulary size to 1000. Note that this is clearly a hyperparameter that deserves some tuning, but a handful of experiments showed that 1000 works well enough *for this specific corpus*.

### Compiling Data into Documents
TBD

## Model Architecture
Let's start by thinking about how to represent the tokens numerically. We'll use an approach known as word embeddings (on the word-level) but apply it to our sub-word tokens. We will embedd the tokens into a 128-dimensional space (yay, another hyperparameter!).
To keep the architecture and implementation as simple as possible, I'm using a Recurrent Neural Network. More specifically, a GRU recurrent layer.