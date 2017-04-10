# Getting-Cleaning-Data
The purpose of this project is to demonstrate your ability to collect, work with, and clean a data set.

Review criteria

The submitted data set is tidy.
The Github repo contains the required scripts.
GitHub contains a code book that modifies and updates the available codebooks with the data to indicate all the variables and summaries calculated, along with units, and any other relevant information.
The README that explains the analysis files is clear and understandable.
The work submitted for this project is the work of the student who submitted it.
Getting and Cleaning Data Course Project

The purpose of this project is to demonstrate your ability to collect, work with, and clean a data set. The goal is to prepare tidy data that can be used for later analysis. You will be graded by your peers on a series of yes/no questions related to the project. You will be required to submit: 1) a tidy data set as described below, 2) a link to a Github repository with your script for performing the analysis, and 3) a code book that describes the variables, the data, and any transformations or work that you performed to clean up the data called CodeBook.md. You should also include a README.md in the repo with your scripts. This repo explains how all of the scripts work and how they are connected.

One of the most exciting areas in all of data science right now is wearable computing - see for example this article . Companies like Fitbit, Nike, and Jawbone Up are racing to develop the most advanced algorithms to attract new users. The data linked to from the course website represent data collected from the accelerometers from the Samsung Galaxy S smartphone. A full description is available at the site where the data was obtained:

http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones

Here are the data for the project:

https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip

You should create one R script called run_analysis.R that does the following.

Merges the training and the test sets to create one data set.
Extracts only the measurements on the mean and standard deviation for each measurement.
Uses descriptive activity names to name the activities in the data set
Appropriately labels the data set with descriptive variable names.
From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.
Analysis

The zip file is downloaded and unzipped. Each of the key text files are read into r with read.table. The data in the Inertial Signals folders are not used for this project.

Each column in the data frame is assigned appropriate labels. As per the readme file for the source data, the features.txt file contains the variable names for the x_train.txt and x_test.txt files. These more descriptive variable names help to make this data more tidy.

The 3 files with train data are combined into one and the 3 files with test data are combined into one by combining columns. The observations (rows) are all matching across each of the 3 files per group.

The 2 resulting data frames are then combined into a single data frame by combining rows. The distinction between being in the test group vs the train group is no longer necessary and no variable remains to identify this.

A subset of the data is pulled with grep() by looking for variable names that include "mean" or "std." Many variables include the letters "mean" but are not actually means, they are mean frequencies. These variables were specifically avoided. In addition to the mean and std vaiables, the first two columns are also kept in the subset. These two columns are our ID columns, the code of the activity engaged in and the subject engaging in the activity.

 # subset columns with mean or std in the column name and include first the columns with general data as well
 data_meanstdsubset <- data_all[,c(1:2,grep("mean[^Freq]|std",colnames(data_all)))]
In order to make the data more easily readable, the activity code (which is a number) is replaced with the descriptive label of that activity. This is accomplished by merging the data with the activity key. Both data frames have variable activitycode. This will add an additional column containing the label. Since the original activtycode column is now essentially a duplicate of the new activitylabel column, the original column is removed to keep with the priciples of tidy data.

# add column to show in descriptive words what activity each activity code is
data_subset_labeled <- merge(data_meanstdsubset, activitylabel, all = TRUE)

# remove duplicative column (activitycode) now that activitylabel is being used
data_subset_labeled <- within(data_subset_labeled, rm(activitycode))
The data is grouped first by activity and then within each activity it is grouped by subject. For each grouping the mean of all observations in those groups are calculated for every variable. This creates the wide version of tidy data.

# Group by activity then by subject and find the mean for each variable
new_tidydata <- aggregate(.~activitylabel+subject, data_subset_labeled, mean)
The results are then written to a text file for submission

# Write data set to a text file
write.table(new_tidydata, "tidydata_getcleandataproject.txt", row.name = FALSE)
To review the results, they can be read in with

 read.table("tidydata_getcleandataproject.txt", header = TRUE)
