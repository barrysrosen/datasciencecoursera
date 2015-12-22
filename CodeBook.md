# Getting and Cleaning Data - Course Project

CodeBook.md describes the variables, data, and any transformations performed to clean up the data.

__Source Data__

* Full description is available at: 
      http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones 
* Data to be used is available at: 
      https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip 

Download includes a compressed zipfile named "UCI HAR Dataset" along with all supporting files:

__README.txt__

* Describes purpose and method used to compile the Human Activity Recognition Using Smartphones Dataset 
* Detail record infomation
* File Listing
* License information

__features_info.txt__

Includes accelerometer and gyroscope 3-axial raw signals and individual measurements. These elements are the basis for the source data. Shows information about the variables used on the feature vector.

__Dataset files__
* 'README.txt'
* 'features_info.txt': Shows information about the variables used on the feature vector.
* 'features.txt': List of all features.
* 'activity_labels.txt': Links the class labels with their activity name.
* 'train/X_train.txt': Training set.
* 'train/y_train.txt': Training labels.
* 'test/X_test.txt': Test set.
* 'test/y_test.txt': Test labels.

__Files available for train and test data__

* 'train/subject_train.txt': Each row identifies the subject who performed the activity for each window sample. Its range is from 1 to 30. 
* 'train/Inertial Signals/total_acc_x_train.txt': The acceleration signal from the smartphone accelerometer X axis in standard gravity units 'g'. Every row shows a 128 element vector. The same description applies for the 'total_acc_x_train.txt' and 'total_acc_z_train.txt' files for the Y and Z axis. 
* 'train/Inertial Signals/body_acc_x_train.txt': The body acceleration signal obtained by subtracting the gravity from the total acceleration. 
* 'train/Inertial Signals/body_gyro_x_train.txt': The angular velocity vector measured by the gyroscope for each window sample. The units are radians/second. 

__Notes__
* Features are normalized and bounded within [-1,1].
* Each feature vector is a row on the text file.

__Steps to process the data__

Get data
```
if(!file.exists("~/00_Coursera/R Programming/WORKING_DIRECTORY")){dir.create("~/00_Coursera/R Programming/WORKING_DIRECTORY")}
fileUrl <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
download.file(fileUrl,destfile="~/00_Coursera/R Programming/WORKING_DIRECTORY/Dataset.zip",method="curl")
```
Unzip file
```
unzip(zipfile="~/00_Coursera/R Programming/WORKING_DIRECTORY/Dataset.zip",exdir="~/00_Coursera/R Programming/WORKING_DIRECTORY")
```
Get list of files from folder
```
file_path <- file.path("~/00_Coursera/R Programming/WORKING_DIRECTORY" , "UCI HAR Dataset")
files<-list.files(file_path, recursive=TRUE)
```
Activity data comes from “Y_train.txt” and “Y_test.txt”
Subject data comes from “subject_train.txt” and subject_test.txt"
Feature values data comes from “X_train.txt” and “X_test.txt”
Feature names data comes from “features.txt”
Levels of Activity come from “activity_labels.txt”

Read Activity files
```
ActivityTestData  <- read.table(file.path(file_path, "test" , "Y_test.txt" ),header = FALSE)
ActivityTrainData <- read.table(file.path(file_path, "train", "Y_train.txt"),header = FALSE)
```
Read Subject files
```
SubjectTestData  <- read.table(file.path(file_path, "test" , "subject_test.txt"),header = FALSE)
SubjectTrainData <- read.table(file.path(file_path, "train", "subject_train.txt"),header = FALSE)
```
Read Features files
```
FeaturesTestData  <- read.table(file.path(file_path, "test" , "X_test.txt" ),header = FALSE)
FeaturesTrainData <- read.table(file.path(file_path, "train", "X_train.txt"),header = FALSE)
```
Merge training and test sets
Concatenate data tables by rows
```
SubjectData <- rbind(SubjectTrainData, SubjectTestData)
ActivityData<- rbind(ActivityTrainData, ActivityTestData)
FeaturesData<- rbind(FeaturesTrainData, FeaturesTestData)
```
Set names to variables
```
names(SubjectData)<-c("subject")
names(ActivityData)<- c("activity")
FeaturesNames <- read.table(file.path(file_path, "features.txt"),head=FALSE)
names(FeaturesData)<- FeaturesNames$V2
```
Merge columns to get dataframe for all data
```
dataCombine <- cbind(SubjectData, ActivityData)
Data <- cbind(FeaturesData, dataCombine)
```
Extract only mean and standard deviation for each measurement
Subset Name of Features by mean and standard deviation
```
subFeaturesNames<-FeaturesNames$V2[grep("mean\\(\\)|std\\(\\)", FeaturesNames$V2)]
```
Subset dataframe by seleted Name of Features
```
selectedNames<-c(as.character(subFeaturesNames), "subject", "activity" )
Data<-subset(Data,select=selectedNames)
```
Read descriptive activity names from “activity_labels.txt”
```
activityLabels <- read.table(file.path(file_path, "activity_labels.txt"),header = FALSE)
```
prefix t is replaced by time
Acc is replaced by Accelerometer
Gyro is replaced by Gyroscope
prefix f is replaced by frequency
Mag is replaced by Magnitude
BodyBody is replaced by Body
```
names(Data)<-gsub("^t", "time", names(Data))
names(Data)<-gsub("^f", "frequency", names(Data))
names(Data)<-gsub("Acc", "Accelerometer", names(Data))
names(Data)<-gsub("Gyro", "Gyroscope", names(Data))
names(Data)<-gsub("Mag", "Magnitude", names(Data))
names(Data)<-gsub("BodyBody", "Body", names(Data))
```
Create tidy data set
```
library(dplyr);
Data2<-aggregate(. ~subject + activity, Data, mean)
Data2<-Data2[order(Data2$subject,Data2$activity),]
write.table(Data2, file = "tidydata.txt",row.name=FALSE)
```

__Variables__

* Project asked for only mean and standard deviation measurements for each observation
     * mean(): Mean value - 33 entries
     * std(): Standard deviation - 33 entries
     * Included angle() variables as they were defined as "vectors obtained by averaging (mean) the signals" - 6 entries
     * Excluded meanFreq() as they were defined as - weighted average of the frequency components to obtain a mean frequency - 13 entries
* Selected Variables
    * Mean() 33 entries
      * tBodyAcc-mean()-X 
      * tBodyAcc-mean()-Y 
      * tBodyAcc-mean()-Z 
      * tGravityAcc-mean()-X 
      * tGravityAcc-mean()-Y 
      * tGravityAcc-mean()-Z 
      * tBodyAccJerk-mean()-X 
      * tBodyAccJerk-mean()-Y 
      * tBodyAccJerk-mean()-Z 
      * tBodyGyro-mean()-X 
      * tBodyGyro-mean()-Y 
      * tBodyGyro-mean()-Z 
      * tBodyGyroJerk-mean()-X 
      * tBodyGyroJerk-mean()-Y 
      * tBodyGyroJerk-mean()-Z 
      * tBodyAccMag-mean() 
      * tGravityAccMag-mean() 
      * tBodyAccJerkMag-mean() 
      * tBodyGyroMag-mean() 
      * tBodyGyroJerkMag-mean() 
      * fBodyAcc-mean()-X 
      * fBodyAcc-mean()-Y 
      * fBodyAcc-mean()-Z 
      * fBodyAccJerk-mean()-X 
      * fBodyAccJerk-mean()-Y 
      * fBodyAccJerk-mean()-Z 
      * fBodyGyro-mean()-X 
      * fBodyGyro-mean()-Y 
      * fBodyGyro-mean()-Z 
      * fBodyAccMag-mean() 
      * fBodyBodyAccJerkMag-mean() 
      * fBodyBodyGyroMag-mean() 
      * fBodyBodyGyroJerkMag-mean()
    * Std() 33 entries
      * tBodyAcc-std()-X 
      * tBodyAcc-std()-Y 
      * tBodyAcc-std()-Z 
      * tGravityAcc-std()-X 
      * tGravityAcc-std()-Y 
      * tGravityAcc-std()-Z 
      * tBodyAccJerk-std()-X 
      * tBodyAccJerk-std()-Y 
      * tBodyAccJerk-std()-Z 
      * tBodyGyro-std()-X 
      * tBodyGyro-std()-Y 
      * tBodyGyro-std()-Z 
      * tBodyGyroJerk-std()-X 
      * tBodyGyroJerk-std()-Y 
      * tBodyGyroJerk-std()-Z 
      * tBodyAccMag-std() 
      * tGravityAccMag-std() 
      * tBodyAccJerkMag-std() 
      * tBodyGyroMag-std() 
      * tBodyGyroJerkMag-std() 
      * fBodyAcc-std()-X 
      * fBodyAcc-std()-Y 
      * fBodyAcc-std()-Z 
      * fBodyAccJerk-std()-X 
      * fBodyAccJerk-std()-Y 
      * fBodyAccJerk-std()-Z 
      * fBodyGyro-std()-X 
      * fBodyGyro-std()-Y 
      * fBodyGyro-std()-Z 
      * fBodyAccMag-std() 
      * fBodyBodyAccJerkMag-std() 
      * fBodyBodyGyroMag-std() 
      * fBodyBodyGyroJerkMag-std()
    * Mean() 6 entries
      * angle(tBodyAccJerkMean),gravityMean) 
      * angle(tBodyGyroMean,gravityMean) 
      * angle(tBodyGyroJerkMean,gravityMean) 
      * angle(X,gravityMean) 
      * angle(Y,gravityMean) 
      * angle(Z,gravityMean) 
    
* Variables NOT selected: 
    * MeanFreq 13 entries 
      * fBodyAcc-meanFreq()-X 
      * fBodyAcc-meanFreq()-Y 
      * fBodyAcc-meanFreq()-Z 
      * fBodyAccJerk-meanFreq()-X 
      * fBodyAccJerk-meanFreq()-Y 
      * fBodyAccJerk-meanFreq()-Z 
      * fBodyGyro-meanFreq()-X 
      * fBodyGyro-meanFreq()-Y 
      * fBodyGyro-meanFreq()-Z 
      * fBodyAccMag-meanFreq() 
      * fBodyBodyAccJerkMag-meanFreq() 
      * fBodyBodyGyroMag-meanFreq() 
      * fBodyBodyGyroJerkMag-meanFreq() 

__Results__
Were written out to the file tidydata.txt 
