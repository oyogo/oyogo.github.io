---
title: "Support Vector Regression using R"
author: "Clinton" 
category: R
tags:
 - r  
 - regression modelling  
 - support vector machine  
 - machine learning  
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE,message=FALSE,warning=FALSE)
```

## SVM(Support Vector Machine)   

Support Vector Machine is a set of supervised machine learning algorithms used to solve classification and regression problems.   

SVM's can be further categorized into two types:  
* SVR (Support vector regression) : for solving regression problems. 
* SVC (Support vector classification): for solving classification problems.  

SVC Performs both linear and non-linear classification: 
Linear: 

Say you have labelled data with a feature which has two groupings (male and female, spam not not-spam), SVMs works in such a way that a line (mostly referred to as a hyperplane) is sought which separates the two distinct labels from each other. The optimal hyperplane would be one whereby the closest label from both group A and group B is as far as possible from the hyperplane (decision boundary) so that the groups are as distinct as possible.   
Its called a decision boundary seeing that its used to decide whether a point falls in group A or group B.  
Now, that works only for a case where the two groups are linearly separable. 

There are cases where the groupings are not linearly separable. 

Enter Non-linear classification: 
The non-linear classification is performed using the kernel function. 
When the dataset is separable by nonlinear boundary, certain kernels are implemented in the SVM to appropriately transform the feature space. 

A kernel is a function that transforms the data into a higher dimensional feature space where data is separable.  

Kernel functions:  
* Linear
* Polynomial 
* Gaussian Radial Basis Function  
* Sigmoid  

The radial basis function kernel is mostly used for non-linear problems. 

SVM's are mostly used in text classification problems.  
One of the gains to using SVM is that it helps to find complex relationships in your data without much transformations.   
Works well in cases where the features are more than the samples. 
Its memory efficient. (uses a subset of the training points in the decision function).  

Cons of SVM's  : 
SVM's don't give probability estimates. You have to calculate.  
Works best on small sample datasets.  
Can be memory consuming especially when processing huge volume of data.  

Dataset information
The data was collected from school reports and questionnaires from two Portuguese schools. The intent is to use it to predict secondary school performance. 
More details on the data can be accessed [here](http://www3.dsi.uminho.pt/pcortez/student.pdf)  

What's covered in this article: 
* Data import  
* Inspecting the data.  
* Data munging and preprocessing.  
* Data partitioning  
* Modelling.  
* Predicting.  
* Model evaluation.  


## Load libraries  
e1071 package has svm function that we can use to build a support vector machine model and the caret package will help us access functions for data partitioning and model evaluation.   

```{r}
library(e1071)
library(caret)
library(dplyr)
library(kernlab)
```

## Import the data   

```{r}
#svm_data <- read.csv("../data/student_performance.csv")
svm_data <- read.csv("https://raw.githubusercontent.com/oyogo/data/main/student_performance.csv")
```

## Data inspection.  
Explore the data to know what data types features are of and if they are correct.  
Number of features and samples. 

```{r}
str(svm_data)
```

## Data preparation and transformation. 
* Encode the categorical variables to numeric type 
You'll often need to encode your features (variables) into numbers as that's what machine learning models understand. 
Your categorical data can either be nominal or ordinal.  

```{r}
svm_data <- svm_data %>% mutate(address = case_when(
  address == "R" ~ 0,
  address == "U" ~ 1
),
famsize = case_when(
  famsize == "LE3" ~ 0,
  famsize == "GT3" ~ 1
),
Pstatus = case_when(
  Pstatus == "T" ~ 0,
  Pstatus == "A" ~ 1
),
Mjob = case_when(
  Mjob == "teacher" ~ 0,
  Mjob == "at_home" ~ 1,
  Mjob == "services" ~ 2,
  Mjob == "other" ~ 3,
  Mjob == "health" ~ 4
),
Fjob = case_when(
  Fjob == "teacher" ~ 0,
  Fjob == "at_home" ~ 1,
  Fjob == "services" ~ 2,
  Fjob == "other" ~ 3,
  Fjob == "health" ~ 4
),
guardian = case_when(
  guardian == "mother" ~ 0,
  guardian == "father" ~ 1,
  guardian == "other" ~ 2
),
schoolsup = case_when(
  schoolsup == "yes" ~ 1,
  schoolsup == "no" ~ 0
),
famsup = case_when(
  famsup == "yes" ~ 1,
  famsup == "no" ~ 0
),
paid = case_when(
  paid == "no" ~ 0,
  paid == "yes" ~ 1
),
activities = case_when(
  activities == "no" ~ 0,
  activities == "yes" ~ 1
),
nursery = case_when(
  nursery == "no" ~ 0,
  nursery == "yes" ~ 1
),
higher = case_when(
  higher == "yes" ~ 1,
  higher == "no" ~ 0
),
internet = case_when(
  internet == "no" ~ 0,
  internet == "yes" ~ 1
),
romantic = case_when(
  romantic == "no" ~ 0,
  romantic == "yes" ~ 1
),
sex = case_when(
  sex == "F" ~ 0,
  sex == "M" ~ 1
)
) 
```

## Feature selection
There are two tests and the final grade that is G1, G2 and G3. 
G1 and G2 have a strong correlation, we will therefore drop one of them, decided to drop G2. This is in keeping with one rule of regression that predictor variables shouldn't be overly correlated, in cases where they are you should drop one of the variables. 

I'll also drop two columns, I felt they aren't needed that much. 

```{r}   

svm_data <- svm_data %>% select(-c("G1","reason","school","X"))   

```

## Data partitioning  
The next step would have been to train and make prediction on the entire data but then the model wouldn't be in a good position to generalize well to data it has not seen. To address that the practice of splitting the data into training and testing sets ensures the model trains on one split of data and then we use a different split to evaluate how the model will perform on data it has not seen.  
One more thing, the data split is not 50/50 by the way, nay, mostly you'll have the data split into about 80/20 or 70/30, just about there, whereby the larger percentage goes to the training set. The idea is to have enough data for the model to train on so that it gets a good understanding of the data. 

We make use of createDataPartition function from caret package to split the data into training and testing set.  
I prefer the 80% 20% partitioning. You could do 70% 30% if you like or any proportion of your liking. 

```{r}

set.seed(123)
indexsvm = createDataPartition(svm_data$G3, p = .8, list = F)
trainsvm = svm_data[indexsvm, ]
testsvm = svm_data[-indexsvm, ]

```

## modelling  using e1071 library  
Here we define the svm model with the default parameters. The default kernel is the radial but you can change it to linear, polynomial or sigmoid.  

```{r}
modelsvm <- e1071::svm(G3 ~ ., data = trainsvm)
summary(modelsvm)
```

## Model evaluation 
For regression problems, we can use RMSE and/or R-Squared metrics to evaluate our model to see how it performs. 
Let's see how our model performs in terms of RMSE and R-Squared metrics.   

```{r}

# Get the predicted values   
pred <- predict(modelsvm, testsvm)

# Get the metrics 
## RMSE 
rmse = RMSE(testsvm$G3, pred)
r2 = caret::R2(testsvm$G3, pred, form = "traditional")

cat("RMSE: ", rmse, "\n",
    "R - Squared: ", r2)
```

## Model using caret  library   
With caret, we can use the train method and pass the following parameters to it.  
1. The target variable regressed on the predictor variables. (training data set). 
2. The data (traiing set). 
3. method = svmRadial : this tells caret to use svm model with a radial basis kernel. 
  if you want to use the linear of polynomial use svmLinear or svmPolynomial.   
  
```{r}
modelsvm2 <- train(
  G3 ~ .,
  data = trainsvm,
  method = "svmRadial"
)
modelsvm2
```


## Preprocessing   
To scale and center our data we'll use preProcess parameter. 

```{r}
modelsvm3 <- train(
  G3 ~ .,
  data = trainsvm,
  method = 'svmRadial',
  preProcess = c('center','scale')
)
modelsvm3
```

## Model3 evaluation  

RMSE and R2 metrics can help us see how the model performs on the test dataset. 

```{r}
pred3 <- predict(modelsvm3, testsvm)

# Get the metrics 
## RMSE 
rmse_model3 = RMSE(testsvm$G3, pred3)
r2_model3 = caret::R2(testsvm$G3, pred3, form = "traditional")

cat("RMSE: ", rmse_model3, "\n",
    "R - Squared: ", r2_model3)
```

## Cross validation   
The concept of cross validation helps us to train our data on splits of data (folds) and then we can pick the best model. 
In caret this is implemented through the parameter : trainControl  

```{r}

set.seed(1) 

control <- trainControl(
  method = "cv",
  number = 10
)


modelsvm4 <- train(
  G3 ~ .,
  data = trainsvm,
  method = "svmRadial",
  preProcess = c("center","scale"),
  trCtrl = control
)
modelsvm4
```


## Model4 evaluation

```{r}
pred4 <- predict(modelsvm4, testsvm)

# Get the metrics 
## RMSE 
rmse_model4 = RMSE(testsvm$G3, pred4)
r2_model4 = caret::R2(testsvm$G3, pred4, form = "traditional")

cat("RMSE: ", rmse_model4, "\n",
    "R - Squared: ", r2_model4)
```

##  Hyperparameter tuning   
In svm regression there are two hyperparameters that we can tune:   

sigma 
cost

```{r}
set.seed(1)

tuneGrid <- expand.grid(
  C = c(0.75,0.9,1, 1.1, 1.25),
  sigma = c(0.01,0.015,0.2)
)

model5 <- train(
  G3 ~ .,
  data = trainsvm,
  method = 'svmRadial',
  preProcess = c("center", "scale"),
  trControl = control,
  tuneGrid = tuneGrid
)
model5
```


## model5 evaluation  
```{r}
pred5 <- predict(model5, testsvm) 

# Get the metrics 
## RMSE 
rmse_model5 = RMSE(testsvm$G3, pred5)
r2_model5 = caret::R2(testsvm$G3, pred5, form = "traditional")

cat("RMSE: ", rmse_model5, "\n",
    "R - Squared: ", r2_model5)
```


Amazing ! 
We have managed to improve our model from an RMSE of 1.71 to 1.5 and R-squared of 0.69 to 0.78. 
That was quite an improvement 


References:     
https://www.datatechnotes.com/2019/09/support-vector-regression-example-with.html    
https://rpubs.com/richkt/280840  
https://www.freecodecamp.org/news/svm-machine-learning-tutorial-what-is-the-support-vector-machine-algorithm-explained-with-code-examples/    
https://scikit-learn.org/stable/modules/svm.html   
https://www.listendata.com/2017/01/support-vector-machine-in-r-tutorial.html  
https://blogs.fu-berlin.de/reseda/support-vector-machine/    


