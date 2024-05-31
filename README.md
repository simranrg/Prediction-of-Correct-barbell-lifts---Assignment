# Prediction-of-Correct-barbell-lifts---Assignment

library(caret)
## Read and explore data 
training = read.csv("C:/Users/ALL/Downloads/pml-training.csv", na.strings=c("NA","#DIV/0!", ""))
View(training)
testing = read.csv("C:/Users/ALL/Downloads/pml-testing.csv", na.strings=c("NA","#DIV/0!", ""))
View(testing)
## to understand the data we check no and types of variables
names(testing)
str(testing)

