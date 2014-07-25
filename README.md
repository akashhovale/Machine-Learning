Machine-Learning Project
================
Problem Statement:
Using devices such as Jawbone Up, Nike FuelBand, and Fitbit it is now possible to collect a large amount of data about personal activity relatively inexpensively. These type of devices are part of the quantified self movement – a group of enthusiasts who take measurements about themselves regularly to improve their health, to find patterns in their behavior, or because they are tech geeks. One thing that people regularly do is quantify how much of a particular activity they do, but they rarely quantify how well they do it. In this project, your goal will be to use data from accelerometers on the belt, forearm, arm, and dumbell of 6 participants. They were asked to perform barbell lifts correctly and incorrectly in 5 different ways. More information is available from the website here: http://groupware.les.inf.puc-rio.br/har (see the section on the Weight Lifting Exercise Dataset). 


 **First, we import the data and take an initial look at it:**

 >if (!file.exists(".pml-testing.csv"))
 
 >{
 >   download.file("https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv",".pml-testing.csv")
 
 >}
 
 >data = read.csv(".pml-testing.csv", na.strings = c("NA", ""))
 
 >dim(data)
 
 >set.seed(0)
 
 >library(caret)
 


 **Next, we will split the data into a training set to train the model on, and a testing set to test the performanace of the model:**
 >inTrain = createDataPartition(y=data$classe, p=0.7, list=FALSE)
 
 >training = data[inTrain,]
 
 >testing = data[-inTrain,]

 >dim(training)

**There are currently 159 variables available to use for training the model. Let’s see if we can pare that number down some by removing variables with a lot of missing values.**

>na_test = sapply(training, function(x) {sum(is.na(x))})
>table(na_test)

**There are 100 columns with almost all missing values. We can remove these columns from our training data and take a look at the remaining columns.**

>bad_columns = names(na_test[na_test==13461])

>training = training[, !names(training) %in% bad_columns]

>str(training)

 **In order to look only at the variables related to movement, we can also remove the first seven variables with have to do with the sequence and subject.**
 
 >training = training[,-c(1:7)]

**We will now create a model to predict the classe using a random forest on the remaining variables (this model took hours to run on my machine, so I saved the model once it was completed so I do not need to rerun the exact model in the future).**

>library(caret)

>library(randomForest)

>model = train(classe~., method="rf", data=training)

>saveRDS(model, "rfmodel.RDS")

>model = readRDS("rfmodel.RDS")

**Once we have trained the model on the training data, we can test the accuracy using the testing data we left out. Let’s define accuracy as the percentage of correct predictions from the model (comparing the predictions from the model to the actual classe variable in the testing data).**

>mean(predict(model, testing) == testing$classe) * 100

**The model is 99.2% accurate on the training data.**
