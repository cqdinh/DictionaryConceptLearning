# DictionaryConceptLearning
A pretraining method for creating a model that generates word embeddings from dictionary definitions

The concept is using a dictionary to pretrain a model that generates a word embedding from it’s definition. This is inspired by the fact that people learn words by being told that “X means Y” 
A dictionary is essentially a database that maps words to all their definitions
In a dictionary, each definition is additionally paired with a type of speech and possibly a hierarchical structure to indicate related meanings.
To get the definitions for words, I am using the WordNet interface in NLTK and extracting all the definitions for each word.

Then, for each word I am generating a definition for each morphological form of the word. For example, the word “bags” gets the definition “plural of bag”. This allows the model to learn the meaning of each of those forms and hopefully to encode the distinction in the embeddings.

People understand definitions based on words they already know, so to train the model, I have it optimize towards producing Word2Vec word embeddings from the definitions of those words. 

However, this is an incomplete training procedure because not all the words in the definitions have Word2Vec embeddings. There are four groupings of words that are used in the training data. They are labeled as:

In WordNet: Has Definition
Not in WordNet: No Definition
In Word2Vec: Has Embedding
Both - Words in WordNet and in Word2Vec - These have both an embedding and definitions
Embedding-only - Words in Word2Vec, but not in WordNet - These only have an embedding
Definition-only - Words in WordNet, but not Word2Vec - These only have a definition
No Information - Words that are not in WordNet or Word2Vec, but which are in the definitions - These have neither a definition nor an embedding


For each word in each definition, the embedding is generated as follows:
	Both & Embedding-only: Embeddings are simply pulled from Word2Vec
  Definition-only: At the beginning of each epoch, embeddings are generated as the model’s output for each word in WordNet which is not in Word2Vec.
	No Information: Embeddings are initialized randomly and optimized directly as trainable variables. These are primarily stop words like “a”, “and”, “the”, and so on as well as punctuation like parentheses, so learning most of them from usage should be viable.

A training epoch consists of running the model over all of the words in Both i.e. words that are present in Word2Vec and have definitions in WordNet. Right now, I’m working on optimizing the training loop so it is faster to train as right now I can’t tell if the model is working.
