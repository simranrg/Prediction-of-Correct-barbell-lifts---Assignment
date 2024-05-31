# Prediction-of-Correct-barbell-lifts---Assignment

## Install and attach libraries
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
```{r configuration, echo=TRUE, results='hide'}
training = read.csv("C:/Users/ALL/Downloads/pml-training.csv", na.strings=c("NA","#DIV/0!", ""))
View(training)
testing = read.csv("C:/Users/ALL/Downloads/pml-testing.csv", na.strings=c("NA","#DIV/0!", ""))
View(testing)
```

### to understand the data we check no and types of variables
```{r configuration, echo=TRUE, results='hide'}
names(testing)
str(testing)
```

### Clean data - to remove any column containing NA values
```{r configuration, echo=TRUE, results='hide'}
training = training[,colSums(is.na(training)) == 0]
testing = testing[,colSums(is.na(testing)) == 0]
```

### Subset data

training = training[,-c(1:7)]
testing = testing[,-c(1:7)]

### Splitting training data for Cross validation
inTrain = createDataPartition(y=training$classe, p=0.75, list=FALSE)
train = training[inTrain, ] 
validation = training[-inTrain, ]

### Exploratory Data Analysis
ggplot(train, aes(x = classe)) +
  geom_bar(fill = "orange") +
  labs(title = "Levels of the variable classe", x = "classe levels", y = "Frequency")
plot(table(train$classe))

###____________DecisionTree____________###

### Fit model,  The type = "class" parameter specifies that class labels should be returned (as opposed to probabilities).
fit = rpart(classe ~ ., data=train, method="class")

### Predict for validation set
prediction = predict(fit, validation, type = "class")

### Plot resultS
rpart.plot(fit, main="Classification Tree", extra=102, under=TRUE, faclen=0)

print(is.factor(validation$classe))
validation$classe=factor(validation$classe)      
confusionMatrix(prediction, validation$classe)
#### KAPPA VALUE: used to assess the performance of a classification model by comparing the predicted classifications to the true classifications.The strength of agreement increases as the kappa value approaches 1. 
#### so the kappa value and accuracy for decision tree is not very significant so we try to fit Random Forest model

###___________RandomForest____________###

### Fit model
train$classe = as.factor(train$classe)
fit_RF = randomForest(classe ~ ., data=train, method="class")

### Predict for validation set
prediction_RF = predict(fit_RF, validation, type = "class")
```{r decisiontreecm, echo=TRUE}
confusionMatrix(prediction_RF, validation$classe)
```
## Prediction for Test dataset
predict_test_RF = predict(fit_RF, testing, type = "class")
predict_test_RF



