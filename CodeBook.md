CodeBook for Tidy Data

The Data

The files in the folder ‘UCI HAR Dataset’ used are:

test/subject_test.txt
train/subject_train.txt
test/X_test.txt
train/X_train.txt
test/y_test.txt
train/y_train.txt
features.txt - Names for the column variables 
activity_labels.txt - describes the class labels with their activity name.

Transformations performed to clean up the data called

First Brought in the dataset into R 

1. Load required packages:

library(tidyr)
library(dplyr)
library(data.table)

2. Combine the data into data frames

# merge datasets and create data tables.
# Build the Training Test
X_test <- read.table("~/JHU/UCI HAR Dataset/test/X_test.txt", quote="\"", comment.char="")
datatest <- tbl_df(X_test)

X_subjecttest <- read.table("~/JHU/UCI HAR Dataset/test/subject_test.txt", quote="\"", comment.char="")
datasubjecttest <- tbl_df(X_subjecttest)

X_ytest <- read.table("~/JHU/UCI HAR Dataset/test/y_test.txt", quote="\"", comment.char="")
dataactivitytest <- tbl_df(X_ytest)


# Build Training DataSet
X_train <- read.table("~/JHU/UCI HAR Dataset/train/X_train.txt", quote="\"", comment.char="")
datatrain <- tbl_df(X_train)

X_subjecttrain <- read.table("~/JHU/UCI HAR Dataset/train/subject_train.txt", quote="\"", comment.char="")
datasubjecttrain <- tbl_df(X_subjecttrain)

X_ytrain <- read.table("~/JHU/UCI HAR Dataset/train/y_train.txt", quote="\"", comment.char="")
dataactivitytrain <- tbl_df(X_ytrain)

#Combine the Activity
allactivity <- rbind(dataactivitytrain, dataactivitytest)
setnames(allactivity, "V1", "activitynumber")

#Combine Subject
allsubject <-  rbind(datasubjecttest,datasubjecttrain)
setnames(allsubject, "V1", "subject")

#Combine Subject and Activity Data
allsubjectactivity <- cbind(allsubject,allactivity)

## Combine the test and training data
alldatatable <- rbind(datatrain,datatest)


##Add Variable Header Names to alldatatable and make them readable 
features <- read.table("~/JHU/UCI HAR Dataset/features.txt", quote="\"", comment.char="")
datavariables <- features$V2
datavariables <-gsub("^t", "time", datavariables)
datavariables<-gsub("^f", "frequency", datavariables)
datavariables<-gsub("Acc", "accelerometer", datavariables)
datavariables<-gsub("Gyro", "gyroscope", datavariables)
datavariables<-gsub("Mag", "magnitude", datavariables)

datavariables<-gsub("BodyBody", "body", datavariables)
datavariables<-gsub("std()", "std", datavariables)
datavariables<-gsub("mean()", "mean", datavariables)
tolower(datavariables)

#add the header values to data table
colnames(alldatatable) <- datavariables
## add the subject and actvity
alldatatable <- cbind(alldatatable, allsubjectactivity)
  
##Activity Labels
activity_labels <- read.table("~/JHU/UCI HAR Dataset/activity_labels.txt", quote="\"", comment.char="")
setnames(activity_labels, "V1", "activitynumber")
setnames(activity_labels, "V2", "activityname")

#Filter out the mean and std variables
meanstdfilter <- grep("mean\\(\\)|std\\(\\)",datavariables,value=TRUE)
meanstdfilter <- c(meanstdfilter,"subject","activitynumber")
alldatatable <- subset(alldatatable, select=meanstdfilter)


4. 
## finally write out the resulting data set
write.table(alldatatable, "tidydata4U.txt", row.name=FALSE)

