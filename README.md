# Prediction-of-Correct-barbell-lifts---Assignment

### Install and attach libraries
```{r configuration, echo=TRUE, results='hide'}

install.packages("caret")
install.packages("rpart")
install.packages("rpart.plot")
install.packages("randomForest")

library(caret)
library(rpart)
library(rpart.plot)
library(randomForest)
```

### Read and explore data 
```
training = read.csv("C:/Users/ALL/Downloads/pml-training.csv", na.strings=c("NA","#DIV/0!", ""))
View(training)
testing = read.csv("C:/Users/ALL/Downloads/pml-testing.csv", na.strings=c("NA","#DIV/0!", ""))
View(testing)
```

### to understand the data we check no and types of variables
```
names(testing)
str(testing)
```

### Clean data - to remove any column containing NA values
```
training = training[,colSums(is.na(training)) == 0]
testing = testing[,colSums(is.na(testing)) == 0]
```

### Subset data
```
training = training[,-c(1:7)]
testing = testing[,-c(1:7)]
```

### Splitting training data for Cross validation
```
inTrain = createDataPartition(y=training$classe, p=0.75, list=FALSE)
train = training[inTrain, ] 
validation = training[-inTrain, ]
```

### Exploratory Data Analysis
```
ggplot(train, aes(x = classe)) +
  geom_bar(fill = "orange") +
  labs(title = "Levels of the variable classe", x = "classe levels", y = "Frequency")
plot(table(train$classe))
```
![Rplot](https://github.com/simranrg/Prediction-of-Correct-barbell-lifts---Assignment/assets/171349867/b20209b9-6e5a-4858-89c3-ec07aaccf438)

## DecisionTree

### Fit model,  The type = "class" parameter specifies that class labels should be returned (as opposed to probabilities).
```
fit = rpart(classe ~ ., data=train, method="class")
```

### Predict for validation set
```
prediction = predict(fit, validation, type = "class")
```

### Plot results
```
rpart.plot(fit, main="Classification Tree", extra=102, under=TRUE, faclen=0)

print(is.factor(validation$classe))
validation$classe=factor(validation$classe)      
confusionMatrix(prediction, validation$classe)
```
![DecTree](https://github.com/simranrg/Prediction-of-Correct-barbell-lifts---Assignment/assets/171349867/e7ca3a01-4e93-4f47-97c4-3c79a8864043)

```
Confusion Matrix and Statistics

          Reference
Prediction    A    B    C    D    E
         A 1169  150   33   35   23
         B   30  543   47   41   47
         C   22   77  531   36   38
         D  147  102  212  585  116
         E   27   77   32  107  677

Overall Statistics
                                          
               Accuracy : 0.7147          
                 95% CI : (0.7019, 0.7273)
    No Information Rate : 0.2845          
    P-Value [Acc > NIR] : < 2.2e-16       
                                          
                  Kappa : 0.6398          
                                          
 Mcnemar's Test P-Value : < 2.2e-16       

Statistics by Class:

                     Class: A Class: B Class: C Class: D Class: E
Sensitivity            0.8380   0.5722   0.6211   0.7276   0.7514
Specificity            0.9313   0.9583   0.9573   0.8593   0.9393
Pos Pred Value         0.8291   0.7669   0.7543   0.5034   0.7359
Neg Pred Value         0.9353   0.9032   0.9229   0.9415   0.9438
Prevalence             0.2845   0.1935   0.1743   0.1639   0.1837
Detection Rate         0.2384   0.1107   0.1083   0.1193   0.1381
Detection Prevalence   0.2875   0.1444   0.1436   0.2369   0.1876
Balanced Accuracy      0.8847   0.7652   0.7892   0.7934   0.8453
```

#### KAPPA VALUE: used to assess the performance of a classification model by comparing the predicted classifications to the true classifications.The strength of agreement increases as the kappa value approaches 1. 
#### so the kappa value and accuracy for decision tree is not very significant so we try to fit Random Forest model

## RandomForest

### Fit model
```
train$classe = as.factor(train$classe)
fit_RF = randomForest(classe ~ ., data=train, method="class")
```

### Predict for validation set
```
prediction_RF = predict(fit_RF, validation, type = "class")
confusionMatrix(prediction_RF, validation$classe)
```
```
Confusion Matrix and Statistics

          Reference
Prediction    A    B    C    D    E
         A 1389    1    0    0    0
         B    4  947    4    0    0
         C    0    1  849    9    0
         D    1    0    2  793    1
         E    1    0    0    2  900

Overall Statistics
                                          
               Accuracy : 0.9947          
                 95% CI : (0.9922, 0.9965)
    No Information Rate : 0.2845          
    P-Value [Acc > NIR] : < 2.2e-16       
                                          
                  Kappa : 0.9933          
                                          
 Mcnemar's Test P-Value : NA              

Statistics by Class:

                     Class: A Class: B Class: C Class: D Class: E
Sensitivity            0.9957   0.9979   0.9930   0.9863   0.9989
Specificity            0.9997   0.9980   0.9975   0.9990   0.9993
Pos Pred Value         0.9993   0.9916   0.9884   0.9950   0.9967
Neg Pred Value         0.9983   0.9995   0.9985   0.9973   0.9998
Prevalence             0.2845   0.1935   0.1743   0.1639   0.1837
Detection Rate         0.2832   0.1931   0.1731   0.1617   0.1835
Detection Prevalence   0.2834   0.1947   0.1752   0.1625   0.1841
Balanced Accuracy      0.9977   0.9979   0.9953   0.9927   0.9991
```
### Expected out-of-sample error
The expected out-of-sample error is estimated at 0.0053, or 0.53%. The expected out-of-sample error is calculated as 1 - accuracy for predictions made against the cross-validation set. Our Test data set comprises 20 cases. With an accuracy above 99% on our cross-validation data, we can expect that very few, or none, of the test samples will be missclassified.

### Prediction for Test dataset
```
predict_test_RF = predict(fit_RF, testing, type = "class")
predict_test_RF
```
```
1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20 
 B  A  B  A  A  E  D  B  A  A  B  C  B  A  E  E  A  B  B  B 
Levels: A B C D E
```


