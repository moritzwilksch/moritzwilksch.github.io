---
layout: post
title:  "Writing Star Trek Scripts: Neural Networks for Text Generation"
date:   2022-01-14 09:45 -0500
tags: neural-network text-generation star-trek
---

With large pre-trained transformer neural networks like GPT-3 or GPT-J becoming more advanced in generating realistic-looking text, I was wondering whether I could train a small, domain-specific model to generate coherent text. Here's what I learned.

## The Task
First things first: We need an interesting corpus of documents to train the model on. I want it to be domain-specific to gauge how well the model is able to pick up on domain-specific concepts, and neither be too small nor too large. Inspired by a friend's suggestion I found [a dump of all Star Trek scripts on Kaggle](https://www.kaggle.com/gjbroughton/start-trek-scripts). So let's build a network that writes *Star Trek: The Next Generation* scripts!

## Preprocessing

### Compiling Data into Documents
The episode scripts have to be split or merged into documents, where each document is a single training data point (i.e. a sequence of words, where the "label" for each word is its successor). These documents should not be too long because long sequences are more computationally expensive to learn and information from the beginning of the sequence tends to get lost by the end. On the other hand, they should also not be too short, as each prediction benefits from the previous context. For this experiment, I decided to concatenate 10 lines into one document (where each line is one characters' part of the dialogue). This gives the model the context of previous parts of dialogue but also keeps sequence lengths short enough to learn efficiently. A limitation of this approach is that the model is not going to be able to capture very long-term relationships in dialogues. However, this is a tradeoff that has to be made at this point: even GPT-3 has a maximum context size of 2048 tokens and cannot attend to an infinitely long sequence of tokens.

### Tokenization
Text generation is generally framed as a next-token prediction task, i.e. the goal is to predict the next token given all previous tokens. That raises the question: What is a token? Usually, tokens are defined as single characters, sub-words, or entire words. Each approach has its pros and cons:

| Method | Advantages | Disadvantages |
| --- | --- | --- |
| **Character-based** | Can generate new words, no out-of-vocab problems, small vocabulary size | Longer training time, can generate jibberish |
| **Subword-based** | Combines advantages from word & character based | Complex vocabulary generation process |
| **Word-based** | Shorter sequence lengths | Out-of-vocabulary problem, cannot generate new words, large vocab size |

Given my limited corpus size and available compute power, I decided to go with the sub-word tokenization approach using a BERT tokenizer. Given a fixed vocab size it combines single characters into subwords for the most frequently occurring patterns in a text. Given that for the Star Trek corpus the character-based tokenization would have had a vocab size of around 90 and the word-based method would have had a vocab size of around 15,000, I decided to set the vocabulary size to 1000. Note that this is clearly a hyperparameter that deserves some tuning, but a handful of experiments showed that 1000 works well enough *for this specific corpus*.


## Model Architecture
Let's start by thinking about how to represent the tokens numerically. We'll use an approach that is known as word embeddings on the word level but apply it to our sub-word tokens. We will embed the tokens into a 128-dimensional space (yay, another hyperparameter!).
To keep the architecture and implementation as simple as possible, I'm using a Recurrent Neural Network, more specifically, a GRU recurrent layer. It copes better with longer sequences than its simpler RNN counterpart and is less computationally expensive than the more complex LSTM. I set the size of the GRU state to be 256-dimensional and feed this vector into a dense layer with 512 relu-activated neurons. Finally, the output layer takes this latent representation and outputs the logits of a probability distribution over all possible 1000 tokens.

## Training
To save time on training I employ the learning rate range test invented and popularized by Smith & Topin (2019). It suggests that the network can be trained with a learning rate of `lr = 0.01`. For the last 25% of the training, I reduce the learning rate by a factor of 10. Note that while this learning rate might seem large, Smith & Topin suggest that it speeds up training significantly without sacrificing the models' ability to converge on a good minimum on the loss surface. The final set of hyperparameters looks as follows:

```yaml
# learning
batch_size: 128
learning_rate: 0.01
epochs: 20
use_scheduler: True

# architecture
embedding_dim: 128
recurrent_size: 256
hidden_size: 512
```


## Text Generation
For generating text from the model we just need to feed it with a couple of seed tokens. These tokens can be passed through the network which in turn outputs the logits of a probability distribution over the possible next tokens. We can now sample from this distribution. I implement a minor adjustment to this process, namely using a temperature of `0.8` in the softmax function that transforms logits to probabilities. This sampling process can be repeated for as many tokens as we need.

## And Finally: The Generated Script
<p align="center" style="font-family:monospace;">
PICARD<br>
Mister LaForge, can you get a frequency to engineering? <br>
<br>
LAFORGE<br>
I'm on my way.<br>
<br>
RIKER<br>
Geordi, I've finished the injury to the holodeck. <br>
<br>
CRUSHER<br>
I'm sorry, sir. we're going to have to succeed. <br>
<br>
PICARD<br>
Then it's a waste of time. I'd like to shut down the mission. I want you to know that I'm right. <br>
<br>
CRUSHER<br>
You look wonderful. I’m really not worried about this mission.<br>
<br>
RIKER<br>
I’ve been very close to caution. what's that? <br>
<br>
(but it's a table)<br>
</p>

## Conclusion
Well, the model does not beat Gene Roddenberry's scripts yet. However, it has learned a couple of remarkable things I'd like to highlight:
- the dialogue structure of the movie scripts
- domain-specific vocabulary and names like *Picard*, *phaser*, or *holodeck*
- dependencies between the different characters' parts of the script: most of the lines are somewhat related to whatever the previous actor mentioned.  

***The source code for this project is [available on GitHub](https://github.com/moritzwilksch/StarTrekWriter)***

## References
1. Smith, L. N., & Topin, N. (2019, May). Super-convergence: Very fast training of neural networks using large learning rates. In *Artificial Intelligence and Machine Learning for Multi-Domain Operations Applications* (Vol. 11006, p. 1100612). International Society for Optics and Photonics.