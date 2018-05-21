---
layout: post
title:  "2018-05-22-Unsupervised-NLP-Text-Analytics"
date:   2018-05-22
categories: how-to
---

# Unsupervised NLP

This article will run through an unsupervised text analytics/NLP project using Python from start to finish. The corpus (a collection of text, documents) we will be using is made up of 47 news articles from various online sources. They are available for download in my github repository.

Here's the general layout of tasks:
1. Import & clean data
2. Tokenize
3. Look a n-gram frequencies
4. Word clustering
5. Document-topic clustering
6. Topic modeling
7. Sentence generation
8. Article summarization
9. Improving Stop Word List


This post won't include much background on the tools being used, but will be topics for different articles that I'll link to once completed.

First thing you'll want to do is open up command prompt or bash and install the following packages. Install one by one, as they have different prompts that you'll need to hit 'y' for to resume download.

```
pip install nltk
pip install linguistica
pip install pyphonetics
```

Once those are installed, fire up a Jupyter Notebook or your preferred programming tool. You might need to finish up the nltk download by running the following:

```python
import nltk
nltk.download()
```

An external window should pop up so try minimizing your browser to find it. You'll wrap up the download in the pop-up window.
## Data Preparation
### Import data & create corpus

```python
path_to_data = 'data/'
newcorpus = PlaintextCorpusReader(path_to_data, '.*txt',encoding='utf8')

# get a list of all of the files
files = list( newcorpus.fileids() )

# get a list of each file's raw text and clean it up
rawText = [newcorpus.raw(f).replace('\r\n',' ').replace('\n',' ') for f in files]

# combine the two lists into one list of lists
# there will be a list of two items for each article, containing file name and raw text
data = [[f,t] for f,t in zip(files,rawText)]

# turn the list of lists into a dataframe
data = pd.DataFrame(data, columns=['filename','rawText'])
```

### Tokenize
#### Stop Words
Create a list of stop words-- words that only serve to create noise in your analysis. The importance of having stop words will be shown later in the post. There's a great list of words I found [online](https://www.ranks.nl/stopwords), which I"ll combine with the list you can get from the nltk package.

```python
# STOP WORDS
# imported list
stop_words_standard = stopwords.words('english')

# long list [source](https://www.ranks.nl/stopwords)
stop_words_long = open('stop_words.txt','r',encoding='utf-8').read().split('\n')

# add all into one list
stop = set(stop_words_standard + stop_words_long)
```

#### Stemming, Punctuation Stripping
Create a stemmer to break down our words, and a function to remove punctuation.

```python
stemmer = PorterStemmer()
re_punct = re.compile('[' + ''.join(string.punctuation) + ']')

```

#### Actual tokenization

We will have 3 lists of tokens.
- __tokens_raw:__ this will be the raw text of the articles, converted to lowercase
- __tokens_clean:__ this takes tokens_raw and then removes words that exist in our list of stop words
- __tokens_stem:__ this takes tokens_clean and converts them into the word's stem

```python
# create the new, empty columns
data['tokens_raw']   = None
data['tokens_clean'] = None
data['tokens_stem']  = None

for row in data.index.tolist():

    # get a raw list of tokens
    text = data.loc[row,'rawText'].lower()             # convert all text to lowercase
    tokens = word_tokenize(text)                       # tokenize the text, meaning turn text into a list of words
    tokens = [re.sub(re_punct, '', t) for t in tokens] # if any of the words have punctuation in them, remove it
    tokens = [t for t in tokens if len(t) > 2]         # if the length of the token is over 2 letters, keep it

    data.loc[row,'tokens_raw'] = ' '.join(tokens)      # set the tokens_raw column to this list of tokens

    # clean up tokens -- remove digits and stop words
    tokens = [re.sub(r'[0-9]', '' , t) for t in tokens] # if any of the words have digits, remove them
    tokens = [t for t in tokens if not t in stop]       # take only the words/tokens that aren't in the list of stop words

    data.loc[row, 'tokens_clean'] = ' '.join(tokens)    # set tokens_clean to the cleaned up list of tokens

    tokens = [stemmer.stem(t) for t in tokens]          # stem the tokens (take the root of the word)

    data.loc[row, 'tokens_stem'] = ' '.join(tokens)     # set tokens_stem to the stemmed tokens
```

['images/chars_words_compare.png]
