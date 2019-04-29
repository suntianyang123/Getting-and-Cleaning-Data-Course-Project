# Getting-and-Cleaning-Data-Course-Project


## 1 Set the work directory.

> setwd("/Users/suntianyang/Documents/R/UCI HAR Dataset")


## 2 Merges the training and the test sets to create one data set.

> X_train <- read.table("./train/X_train.txt")
> X_test <- read.table("./test/X_test.txt")
> X_subject <- rbind(X_train, X_test)


## 3 Extracts only the measurements on the mean and standard deviation for each measurement.

> features <- read.table("features.txt")
> ms_col <- grep("mean\\(|std\\(", features$V2)
> subject_mean_std <- X_subject[, ms_col]


## 4 Uses descriptive activity names to name the activities in the data set.

> y_train <- read.table("./train/y_train.txt")
> y_test <- read.table("./test/y_test.txt")
> library(stringr)
> activity <- str_replace_all(rbind(y_train, y_test)$V1,
>                             c("1" = "walking", "2" = "walking_upstairs",
>                               "3" = "walking_downstairs", "4" = "sitting",
>                               "5" = "standing", "6" = "laying"))
> subject_mean_std <- cbind(activity, subject_mean_std)


## 5 Appropriately labels the data set with descriptive variable names.

var_names <- as.character(features[ms_col, ]$V2)
colnames(subject_mean_std)[-1] <- var_names


## 6 From the dataset in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.

subject_train <- read.table("./train/subject_train.txt")
subject_test <- read.table("./test/subject_test.txt")
subject <- rbind(subject_train, subject_test)
colnames(subject) <- "subject"
subject_mean_std <- cbind(subject, subject_mean_std)
library(dplyr)
data <- tbl_df(subject_mean_std)
result <- data %>%
        arrange(subject, activity) %>%
        group_by(subject, activity) %>%
        summarize_all(.fun = mean)
        

## 7 Print the result.

print(result)
