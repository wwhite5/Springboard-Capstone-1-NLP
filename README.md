# Springboard-Capstone-1-NLP
A natural language processing project

# Buisness Case
 Plagiarism in scientific writing has been a difficult problem to solve, with many scientific journals publishing articles from “paper mills” that were not written by their supposed authors. For example, “International Publisher Limited” is a Russian paper mill company that offers authorship on soon to be released scientific papers [for up to $5000](https://www.science.org/content/article/russian-website-peddles-authorships-linked-reputable-journals). While these operations often target less reputable journals, even reputable publishing houses such as Elsevier have published articles from these mills. Natural language processing as a machine learning technique has progressed greatly over the past few years, and may offer a potential solution. By developing a model that can read a paper’s text and determine likely authorship, this kind of academic fraud could be caught more easily.

# Overview

# Data Source
https://www.kaggle.com/datasets/benhamner/nips-papers

# Data Wrangling
The dataset consists of three csv files: "authors.csv", "papers.csv", and "paper_authors.csv" that joins the other two on author_id and paper_id. papers.csv contains the text of the paper in question, as well as its title, abstract, year published, and its "event type" (poster, oral presentation ect.). Four of the papers had duplicated paper_text fields, but these were left in and dealt with in a later notebook. authors.csv is the plaintext name of the author of the paper, as well as a numeric author_id. Some identical author names had different author_ids, including the most prolific author in the dataset. This was corrected, and a new .csv was created with the author_id and all of the information on the papers.csv in one place. One issue is that there is no record of who was the primary author for any paper, which may have been very predictive.

# Predicting an Author from the Title
In this notebook, I looked at trying to predict if one person, in this case Bernhard Scholkopf, was the author of a paper based only on its title. This involved using scikit-learn's Tfidf Vectorizer to convert the words in the titles to numbers (vectors). The vectorizer turns each word in each title into a new feature, with the values being the relative frequency those words appear in each document. This creates a very sparse matrix of new numeric features (4865 in this case) and importantly is a "bag of words" representation, where the order and context of the words is not kept. It is, however, reletively computationally inexpensive compared to more complex deep learning approaches. 

One of the major problems with this dataset is how imbalanced it is, the most prolific author only accounts for ~1.5% of the dataset, so when predicting for Bernhard's papers I tried class weighting, oversampling, and undersampling strategies in order to see what effect they had on the model.

# Predicting One Author from the Paper Text
Predicting one author from the text of the paper was broken into two notebooks, the first focused on creating new features (title length, paper length, and average word length) then fitting a simple logistic regression model to all of those features. This gave an AUC score of ~0.71, but checking performance on the train set indicated there may be some overfitting. I used GridSearchCV to check multiple values of the regularization parameter "C", but that returned the same value of C I had been using before, I wanted to check if that was accurate myself. In addition, using different train/test splits created large diffenences in model performance, which the following notebook would attempt to account for.

This notebook features checks of logistic regression, LinearSVD, and Random Forest models in a "manual" grid search that allowed me to check individual model performance across multiple hyperparameters and against each-other, using F1-score as the metric to judge performance. 

The logistic regression model was found to give the best performance, so I decided to do some further experiments on it. I used TruncatedSVD for dimentionality reduction (similar to PCA but it works on sparce matricies like mine) in order to do some basic topic modeling, but I was unable to use more than 1000 components on my machine, and everything 1000 components or less led to very poor model performance. I also did "manual" cross validating on the logistic regression model, in order to get an idea of performance across several train/test splits. This gave me an average F1 score of 0.4 on the best model, which had a regularization parameter C=1 and used random oversampling. 

Finally, I wanted to check feature importance for the final model, and used predict_proba to get the predicted probabilities of the negative and positive cases for the papers. While confusion matricies and hard yes/no answers are useful for comparing models to each other, the probability a given author wrote a paper would better suit the real world use case of the model, and shows a middle ground of "possibly this author's papers" that would otherwise be lost.

# Predicting Multiple Authors at Once