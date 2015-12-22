# Getting and Cleaning Data - Course Project
This repository contains R code and documentation files for the course "Getting and Cleaning Data" available in Coursera.

Dataset used: [Human Activity Recognition Using Smartphones] (https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip)

CodeBook.md describes the variables, data, and any transformations performed to clean up the data.

run_analysis.R contains all code to perform the analyses described in the following 5 steps:
* Merge  training and test sets to create one data set.
* Extract only the measurements for mean and standard deviation. 
* Use descriptive activity names to name the activities in the data set
* Appropriatel label the data set with descriptive variable names. 
* Create a second, independent tidy data set with the average of each variable for each activity and each subject.

tidydata.txt is the output data set from running run_analysis.R.
