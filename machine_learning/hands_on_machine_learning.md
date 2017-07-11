# 1. The Machine Learning Landscape
- Optical Character Recognition (OCR)
- spam filter

## What Is Machine Learning?
- the science (and art) of programming computers so they can learn from data
- general definition
    - field of study that gives computers the ability to learn without being explicitly programmed
- engineering-oriented definition
    - a computer program is said to learn from experience E with respect to some task T and some
      performance measure P, if its performance on T, as measured by P, improves with experience E

## Why Use Machine Learning?
- automatic adaptation to modified input without the need for manual reprogramming
- good performance in areas where the solution is not strictly defined
- good for problems that are too complex for traditional approaches or have no known algorithm
- discovery of patterns in data mining of large data sets that were not immediately apparent

## Types of Machine Learning Systems
- criteria
    - trained under human supervision
        - supervised
        - unsupervised
        - semisupervised
        - Reinforcement Learning
    - learn incrementally or on the fly
        - online
        - batch learning
    - compare new data points to known data points or detect patterns and build predictive model
        - instance-based learning
        - model-based learning

### Supervised/Unsupervised Learning
- supervised
    - training data fed to the algorithm includes the desired solutions
    - labels - desired solutions
    - classification - train with many examples and model learns how to classify
    - regression - predict a target value when given a set of features called predictors
    - important algorithms
        - k-nearest neighbors
        - linear regression
        - logistic regression
        - support vector machines (SVMs)
        - decision trees and random forests
        - neural networks
- unsupervised learning
    - training data is unlabeled
    - important algorithms
        - clustering
            - k-means
            - hierarchical cluster analysis (HCA)
            - expectation maximization
        - visualization and dimensionality reduction
            - principal component analysis (PCA)
            - kernel PCA
            - locally-linear embedding (LLE)
            - t-distributed stochastic neighbor embedding (t-SNE)
        - association rule learning
            - apriori
            - eclat
     - clustering - detect groups of similarities
     - hierarchical clustering - subdivides detected groups into subgroups
     - visualization algorithms are a form of unsupervised learning
     - dimensionality reduction - simplify data without losing too much information
     - feature extraction
     - TIP: often good to perform dimensionality reduction on data before feeding it
       to another ML algorithm
     - anomaly detection
     - association rule learning - discover interesting relationships between attributes
- semisupervised learning
    - some partially labeled and some unlabeled data
    - deep belief networks (DBNs)
        - restricted Boltzmann machines (RBMs)
- reinforcement learning
    - learning system (agent) observes environment and performs actions
        - rewards or penalties are given for actions
        - system learns by itself to select a policy (or strategy)

### Batch and Online Learning
- batch
    - system is incapable of learning incrementally
    - must be trained using the full data set, generally offline
    - offline learning - trained and then launched to production
- online learning
    - train system incrementally either by feeding it data instances sequentially or
      in small groups called mini-batches
    - good for systems receiving a continuous flow of data
    - also good for datasets that cannot fit in one machine's memory
    - online learning is generally done offline! (think of it as incremental learning)
    - learning rate - important parameter - how fast system should adapt to changing data
        - high learning rate - system will rapidly adapt to new data, but it will also tend to quickly forget
          the old data
          (you don’t want a spam filter to flag only the latest kinds of spam it was shown).
        - low learning rate - system will learn more slowly (more inertia), but it will also be less sensitive to
          noise in the new data or to sequences of nonrepresentative data points
    - challenge with online learning - if bad data is fed to the system, the system’s performance will gradually decline

### Instance-Based Versus Model-Based Learning
- how the system generalizes
- instance-based learning
    - system learns by recording information then generalizes to new cases using a similarity
      measure
- model-based learning
    - build a model from the examples and then use model to make predictions
    - noisy data
    - model selection
    - linear model
    - model parameters
    - utility/fitness function
    - linear regression and training the model
    - process
        - study data
        - select model
        - train model on data (learning algorithm searched the model parameter values that
          minimize a cost function)
        - apply model to make predictions on new cases (inference)

## Main Challenges of Machine Learning
- bad algorithm (poor algorithm selection)
- bad data
- insufficient quality of training data
    - typically need a large amount of data
    - famous paper showed that vary different (and even vary simple) ML algorithms perform
      almost identically well for complex problems (natural language) when given enough data
- nonrepresentative training data
    - need data that is representative of the new instances that you want to generalize to for
      predictions
    - small sample results in sampling noirs
    - sampling bias
- poor quality data
- irrelevant features
    - feature engineering
        - feature selection - selecting the most useful features to train on among existing features
        - feature extraction - combining existing features to produce a more useful one (dimensionality reduction
          algorithms can help)
        - creating new features by gathering new data
- overfitting the training data
    - overgeneralization
    - overfitting happens when the model is too complex relative to the amount and noisiness of the training data
        - solutions
            - simplify the model by selecting one with fewer parameters (e.g., a linear model rather than a high-degree
              polynomial model), by reducing the number of attributes in the training data or by constraining the model
            - gather more training data
            - reduce the noise in the training data (e.g., fix data errors and remove outliers)
    - regularization - constraining a model to make it simpler and reduce the risk of overfitting
- underfitting the training data
    - solutions
        - select a more powerful model, with more parameters
        - feed better features to the learning algorithm (feature engineering)
        - reduce the constraints on the model (e.g., reducing the regularization hyperparameter)
- overview
    - machine Learning is about making machines get better at some task by learning from data, instead of having to
      explicitly code rules
    - there are many different types of ML systems
        - supervised vs unsupervised
        - batch vs online
        - instance-based vs model-based
    - in an ML project you gather data in a training set, and you feed the training set to a learning algorithm
    - if the algorithm is model-based it tunes some parameters to fit the model to the training set (i.e., to make good
      predictions on the training set itself), and then hopefully it will be able to make good predictions on new cases
      as well
    - if the algorithm is instance-based, it just learns the examples by heart and uses a similarity measure to
      generalize to new instances
    - the system will not perform well if training set is too small, or if the data is not representative, noisy,
      or polluted with irrelevant features (garbage in, garbage out)
    - the model needs to be neither too simple (in which case it will underfit) nor too complex (in which case it will
      overfit)

## Testing and Validating
- generally not great to put model into production then observe performance
- split data
    - training set - %80
    - test set - %20
- generalization error (out-of-sample error)
- train model on test set to get an estimate of generalization error
- if the training error is low (i.e., your model makes few mistakes on the training set) but the generalization error
  is high, it means that your model is overfitting the training data
- if a model generalizes better but needs regularization
    - for regularization hyperparemeter
        - train 100 models using 100 values
            - problem - measured the generalization error multiple times on the test set, and
              adapted the model and hyperparameters to produce the best model for that set
            - model is unlikely to perform as well on new data
         - use a validation set
            - train multiple models with various hyperparameters using the training set
            - select the model and hyperparameters that perform best on the validation set
            - when model seems to perform well, run a single final test against the test set to get an estimate of the
              generalization error
            - use cross-validation to avoid wasting too much training data in validation sets
            - cross-validation
                - the training set is split into complementary subsets, and each model is trained against
                  a different combination of these subsets and validated against the remaining parts
                - once the model type and hyperparameters have been selected, a final model is
                  trained using these hyperparameters on the full training set, and the generalized
                  error is measured on the test set


# 2. End-toEnd Machine Learning Project
- process summary
    - get an overview of project
    - retrieve data
    - discover and visualize the data to gain insights
    - prepare the data for ML algorithms
    - select a model and train it
    - fine-tune model
    - present solution
    - launch, monitor, and maintain system

## Working with Real Data
- popular open data repositories
    - UC Irvine Machine Learning Repository
    - Kaggle datasets
    - Amazon’s AWS datasets
- meta portals (they list open data repositories):
    - http://dataportals.org/
    - http://opendatamonitor.eu/
    - http://quandl.com/
- ther pages listing many popular open data repositories
    - Wikipedia’s list of Machine Learning datasets
    - Quora.com question
    - Datasets subreddit

## Look at Big Picture
- see ML project checklist in appendix B
- frame the problem
    - business objectives etc.
- select a performance measure
    - root mean square (RMSE)
    - mean absolute error (MAE)
    - norms
        - Euclidean norm
        - Manhattan norm
- check the assumptions

## Get the Data
- create the workspace
    - Jupyter
    - virtualenv
- download the data
    - script it (urllib)
- observe structure of data
- create a test set

## Discover and Visualize the Data to Gain Insights
- matplotlib, Pandas, D3.js, etc

## Prepare the Data for Machine Learning Algorithms
- TODO: more here
- write functions to prepare data for reuse and automation
- data cleaning
- handling text and categorical attributes
- custom transformers
- feature scaling
- transformation pipelines

## Select and Train a Model
- TODO: more here
- training and evaluating on the training set
- better evaluation using cross-validation

## Fine Tune Your Model
- grid search
- randomized search
- ensemble methods
- analyze the best models and their errors
- evaluate system on the test set

## Launch, Monitor, and Maintain Your System


# 3. Classification
## MNIST
- 70,000 small images of handwritten digits
- commonly used for testing
- sklearn datasets
    - DESCR - description of dataset
    - data - array of one row per record and one column per feature
    - target - key containing array with labels

## Training a Binary Classifier
- binary classifier - capable of distinguishing between just two classes
- stochastic gradient descent (SGD)
    - stochastic - randomly determined, having a random probability distribution
    - good starting point for binary classifier
    - capable of handling large datasets efficiently
    - trains instances independently
    - well suited for online learning
```
from sklearn.linear_model import SGDClassifier

sgd_clf = SGDClassifier(random_state = 42)
sgd_clf.fit(X_train, y_train_5)
sgd_clf.predict([some_digit])
```

## Performance Measures
- classifiers are often more difficult to evaluate than regressors

### Measuring Accuracy Using Cross-Validation
- may need more control than sklearn's built-in cross-validation support
```
from sklearn.model_selection import StratifiedKFold
from sklearn.base
import clone

skfolds = StratifiedKFold(n_splits = 3, random_state = 42)
for train_index, test_index in skfolds.split(X_train, y_train_5):
    clone_clf = clone(sgd_clf)
    X_train_folds = X_train[train_index]
    y_train_folds = (y_train_5[train_index])
    X_test_fold = X_train[test_index]
    y_test_fold = (y_train_5[test_index])
    clone_clf.fit(X_train_folds, y_train_folds)
    y_pred = clone_clf.predict(X_test_fold)
    n_correct = sum(y_pred == y_test_fold)
    print(n_correct / len(y_pred))  # prints 0.9502, 0.96565 and 0.96495
```
- k-fold cross-validation - split training set into k-folds then make predictions and evaluate
  on each fold using a model trained on the remaining folds
- be aware that accuracy is generally not the preferred performance measure for classifiers
    - especially not for skewed datasets (some classes are more frequent than others)

### Confusion Matrix
- counts of the number of times class A was confused and classified as class B (with counts
  written to the corresponding matrix cell)
    - each row represents and actual class
    - each column represents a predicted class
    - negative class, true positives, true negatives, false positives, false negatives
- precision of classifier - accuracy of positive predictions
```
precision = TP / (TP + FP)
TP - true positives
FP - false positives
```
- recall (sensitivity or true positive rate (TPR)) - ratio of positive instances that are
  correctly detected by the classifier
```
recall = TP / (TP + FN)
FN - false negatives
```

### Precision and Recall
- sklearn provides many classifier metrics functions (including precision and recall)
```
from sklearn.metrics import precision_score, recall_score
```
- F1 score - harmonic mean of precision and recall
    - whereas the regular mean treats all values equally, the harmonic mean gives much more
      weight to low values
    - the classifier will only get a high F1 score if both recall and precision are high
```
F1 = 2 / ((1 / precision) + (1 / recall)) = 2 * (precision * recall) / (precision + recall) = TP / (TP + (FN + FP) / 2)
```
- importing
```
from sklearn.metrics import precision_score, recall_score
```
- precision/recall tradeoff - increasing precision reduces recall and vice versa

### Precision/Recall Tradeoff
- decision function -
- to make classification decisions SGD classifier computes a score based on the decision function and if that score is
  greater than a threshold, it assigns the instance to the positive class, or else it assigns it to the negative class
- decision threshold
- sklearn does not allow setting threshold directly but can access the value
- can plot precision and recall as functions of the threshold value using matplotlib
- increasing precision requires setting a recall

### The ROC Curve
- receiver operating characteristics (ROC) - plots true positive rate (another name for recall) against
  the false positive rate
    - FPR - ratio of negative instances that are incorrectly classified as positive ( =  1 - TNR)
    - TNR (specificity) - ratio of negative instances that are correctly classified as negative
    - ROC = sensitivity (recall) vs. 1 - specificity
- similar to precision/recall curve
- as a rule of thumb, prefer the PR curve whenever the positive class is rare or when you care
  more about the false positives than the false negatives, and the ROC curve otherwise

## Multiclass Classification
- aka multinomial classifiers - used to distinguish between more than two classes
- can handle multiples classes directly
    - random forest classifiers
    - naive Bayes classifiers
    - + more
- only binary
    - support vector machine classifiers
    - linear classifiers
- can use various strategies to perform multiclass classification using binary classifiers
- one-versus-all (OvA) strategy (one-versuse-the-rest)
- one-versus-one (OvO)
- sklearn auto-detects when a binary classifier is used for multiclass classification and
  runs OvA (except for SVM which uses OvO)
- WARNING: when a classifier is trained, it stores the list of target classes in its classes_ attribute,
  ordered by value
```
from sklearn.multiclass import OneVsOneClassifier
```

## Error Analysis
- to improve models, first analyze the types of errors that they make
- check confusion matrix
    - often easier to view as an image
- with MNIST, can process the images
    - Scikit-Image
    - Pillow
    - OpenCV
- analyzing individual errors can be useful but time consuming

## Multilabel Classification
- trained on multiple labels and outputs set of binary values for each label
- there are many ways to evaluate a multilabel classifier, and selecting the right metric
  depends on the project
    - one approach is to measure the F1 score for each individual label (or any other binary classifier
      metric discussed earlier), then simply compute the average score

## Multioutput Classification
- multioutput-multiclass classification (or multioutput classification) - a generalization of multilabel classification
  where each label can be multiclass (i.e., it can have more than two possible values)
- NOTE: The line between classification and regression is sometimes blurry
    - predicting pixel intensity is more akin to regression than to classification
    - multioutput systems are not limited to classification tasks
    - can have a system that outputs multiple labels per instance, including both class labels and value labels


# Machine Learning Project Checklist
## process summary
1. get an overview of project
2. retrieve data
3. discover and visualize the data to gain insights
4. prepare the data for ML algorithms
5. select a model and train it
6. fine-tune model
7. present solution
8. launch, monitor, and maintain system

## 1. get an overview of project
1. define the objective in business terms
    - how will the solution be used?
    - what are the current solutions/workarounds (if any)?
    - how should this problem be framed (supervised/unsupervised, online/offline, etc.)?
    - how should performance be measured?
    - is the performance measure aligned with the business objective?
    - what would be the minimum performance needed to reach the business objective?
    - what are comparable problems?
    - can you reuse experience or tools?
    - is human expertise available?
    - how would you solve the problem manually?
2. list the assumptions made
3. verify assumptions if possible

## 2. retrieve data
- NOTE: automate as much as possible
1. list the data needed and how much is needed
2. find and document where you can get that data
3. check how much space it will take
4. check legal obligations, and get authorization if necessary
5. get access authorizations
6. create a workspace (with enough storage space)
7. get the data
8. convert the data to a format you can easily manipulate (without changing the data itself)
9. ensure sensitive information is deleted or protected (e.g., anonymized)
10. check the size and type of data (time series, sample, geographical, etc.)
11. sample a test set, put it aside



## 3. discover and visualize the data to gain insights
- prepare the data for ML algorithms
- select a model and train it
- fine-tune model
- present solution
- launch, monitor, and maintain system

