---
title: "README.md"
author: "Patil Harshal Yashwant"
date: "1 July 2020"
output:
  word_document: default
  pdf_document: default
  html_document: default
---
This part of script downloads the file and stores it at specific location.

Specify URL where file is stored
```
url <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
```
Specify destination where file should be saved
```
wd<- getwd()
destfile<- paste(wd,"/Assignment_module_03.zip", sep="")
```
Apply download.file function in 
```
download.file(url, destfile)
```
This part of scripts unzips the zipped file and saves the files in perticular location in given eternal directory
unzip the file
```
unzip("C:/Users/harsh/Documents/Assignment_module_03.zip", files = NULL, list = FALSE, overwrite = TRUE,junkpaths = FALSE, exdir = '.', unzip = "internal",setTimes = FALSE)
```
Now we have number of .txt unzipped files which we need to read into r object. The following chunck of code does the exact same job.
```
destfile_activity_labels<- paste(wd,"/UCI HAR Dataset/activity_labels.txt", sep="")
activity_labels<- read.table(destfile_activity_labels)
destfile_features<- paste(wd,"/UCI HAR Dataset/features.txt", sep="")
features<- read.table(destfile_features)
destfile_subject_test<- paste(wd,"/UCI HAR Dataset/test/subject_test.txt", sep="")
subject_test<- read.table(destfile_subject_test)
destfile_X_test<- paste(wd,"/UCI HAR Dataset/test/X_test.txt", sep="")
X_test<- read.table(destfile_X_test)
destfile_y_test<- paste(wd,"/UCI HAR Dataset/test/y_test.txt", sep="")
y_test<- read.table(destfile_y_test)
destfile_subject_train<- paste(wd,"/UCI HAR Dataset/train/subject_train.txt", sep="")
subject_train<- read.table(destfile_subject_train)
destfile_X_train<- paste(wd,"/UCI HAR Dataset/train/X_train.txt", sep="")
X_train<- read.table(destfile_X_train)
destfile_y_train<- paste(wd,"/UCI HAR Dataset/train/y_train.txt", sep="")
y_train<- read.table(destfile_y_train)
```
Here comes the part of script that performs analysis on raw data and converts it into tidy dataset
combining test data and train data into one dataset
```
X_train_M<- as.matrix(X_train)
colnames(X_train_M) <- as.vector(features$V2)
X_train<- as.data.frame(X_train_M)
```
merging y-train and activity_labels to get names for corresponding activities
```
merge_data_activityLabels_ytrain = merge(y_train,activity_labels, sort = FALSE)
X_train$activity_labels<- merge_data_activityLabels_ytrain$V2
```
merging subject_train and X-train to get numbers for corresponding subjects
```
X_train$subject<- subject_train$V1
```
concatening x-test y_test and subject_test into test_data 
```
X_test_M<- as.matrix(X_test)
colnames(X_test_M) <- as.vector(features$V2)
X_test<- as.data.frame(X_test_M)
```
merging y-test and activity_labels to get names for corresponding activities
```
merge_data_activityLabels_ytest = merge(y_test,activity_labels, sort = FALSE)
X_test$activity_labels<- merge_data_activityLabels_ytest$V2
```
merging subject_train and X-train to get numbers for corresponding subjects
```
X_test$subject<- subject_test$V1
```
merge train data and test data into one dataset
```
data_set_one<- rbind(X_train, X_test)
```
This part of script extracts the mean and stadard deviation readings from all the readings
```
data_set_02<- data_set_one[ , grepl( "mean" , names( data_set_one ) ) ]
data_set_03<- data_set_one[ , grepl( "std" , names( data_set_one ) ) ]

data_set_02$activity_labels<- data_set_one$activity_labels
data_set_02$subject<- data_set_one$subject

data_set_03$activity_labels<- data_set_one$activity_labels
data_set_03$subject<- data_set_one$subject

data_set= merge(data_set_02, data_set_03, sort = FALSE)
```
This part groups the data as per subject and activity and calculates mean 
```
data_set_05<- data_set%>% group_by(activity_labels, subject) %>% summarise(across(.cols = everything(), mean))
```
Write this dataset into a txt file with given name in working directory
```
write.table(data_set_02,file="tidy_data_set.txt", row.name= FALSE)
```
