---
layout: post
title:  "Text Analytics & Unsupervised NLP - Part II"
date:   2018-05-22
categories: how-to
---

In the first article of the Text Analytics & Unsupervised NLP series, I walked through importing your data, stop words, cleaning, and tokenizing and finished off with visualizing n-grams: uni-, bi- and tri-grams.

This article will focus on a few different applications of clustering. First, we'll generate clusters of similar words across the corpus, then we'll create a dendrogram to represent document-clusters based on topic, and finally we'll do some topic modeling.

## Load Data
Refer to the first article to load your data. This will pick up right where part I left off.

## Word Clusters
Cluster words from the corpus into different groups.

Instead of using our tokenized lists of words, we'll be using a [TfidfVectorizer](http://scikit-learn.org/stable/modules/generated/sklearn.feature_extraction.text.TfidfVectorizer.html#sklearn.feature_extraction.text.TfidfVectorizer). In a nutshell, tfidf (term frequency-inverse document frequency) aims to find the most important words in a corpus. The rough calculation is below: 
- Term frequency = frequency of word in document / total number of words in document
- Inverse document frequency = log_e(# of documents in corpus / number of documents with the particular word)
TF-IDF = term frequency * inverse document frequency
TF-IDF can be helpful, alongside a list of stop words, to determine the most important words. The more important the word, the higher the TF-IDF score. A low score would mean that the word appears frequently across all documents, which may signify that it’s unimportant and should be excluded from analysis.

So TF-IDF will help us build a vocabulary full of significant words (relatively speaking.) We'll then use KMeans to cluster the words.


```python
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.cluster import KMeans
from sklearn.metrics import adjusted_rand_score

documents = data['rawText'].tolist()    

# max_df = cutoff score. words with higher scores are more rare
# min_df = cutoff score. words with lower score won't be included as they are too common
# max_features = # of top words to include within threshold
vectorizer = TfidfVectorizer(max_df=0.95, min_df=2, max_features=1000, stop_words='english')
X = vectorizer.fit_transform(documents)
 
n = 2 # number of clusters to make
model = KMeans(n_clusters=n, init='k-means++', max_iter=100, n_init=1,random_state=100)
model.fit(X)
 
print("Top terms per cluster:")
print('----------------------')
order_centroids = model.cluster_centers_.argsort()[:, ::-1]
terms = vectorizer.get_feature_names()
for i in range(n):
    print("Cluster %d:" % i),
    for ind in order_centroids[i, :10]:
        print(' %s' % terms[ind])
    print()
```

```
Top terms per cluster:
----------------------
Cluster 0:
 trump
 president
 fox
 mr
 agency
 house
 abc
 white
 administration
 tuesday

Cluster 1:
 gun
 people
 says
 students
 food
 new
 mr
 company
 years
 school
 ```
 
 Now lets feed sentences into our KMeans model to predict which cluster it belongs in. The sentence will be vectorized with the TF-IDF vectorizer and then fed into the model.
 
 ```python
sentence1 = "some people drink a lot of coffee"
sentence2 = "trump lives in the white house"

print("Prediction")
 
Y = vectorizer.transform([sentence1])
prediction = model.predict(Y)
print(sentence1,'belongs in: cluster', prediction[0])
 
Y = vectorizer.transform([sentence2])
prediction = model.predict(Y)
print(sentence2,'belongs in: cluster', prediction[0])
```

```python
Prediction
some people drink a lot of coffee belongs in: cluster 1
trump lives in the white house belongs in: cluster 0
```

Pretty cool!


## Document Clustering (based on topic)

