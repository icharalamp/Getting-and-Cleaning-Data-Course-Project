_The following info file describes the scirpt wearable_Script.R_



# 1. Set working directory
setwd("C:/Users/Ioannis/Desktop")

#Load the apropriate libraries#

# 2.Load library
library(data.table)
library(reshape2)


# 3. Load activity labels
activity_labels <- read.table("./UCI HAR Dataset/activity_labels.txt")[,2]


# 4. Load data column names
features <- read.table("./UCI HAR Dataset/features.txt")[,2]


# 5. Extract only the measurements on the mean and standard deviation for each measurement
extract_features <- grepl("mean|std", features)


# 6. Load and process X_test & y_test data.
X_test <- read.table("./UCI HAR Dataset/test/X_test.txt")
y_test <- read.table("./UCI HAR Dataset/test/y_test.txt")
subject_test <- read.table("./UCI HAR Dataset/test/subject_test.txt")

names(X_test) = features


# 7. Extract only the measurements on the mean and standard deviation for each measurement
X_test = X_test[,extract_features]


# 8. Load activity labels
y_test[,2] = activity_labels[y_test[,1]]
names(y_test) = c("Activity_ID", "Activity_Label")
names(subject_test) = "subject"


# 9. Bind data
test_data <- cbind(as.data.table(subject_test), y_test, X_test)


# 10. Load and process X_train & y_train data.
X_train <- read.table("./UCI HAR Dataset/train/X_train.txt")
y_train <- read.table("./UCI HAR Dataset/train/y_train.txt")

subject_train <- read.table("./UCI HAR Dataset/train/subject_train.txt")

names(X_train) = features


# 11. Extract only the measurements on the mean and standard deviation for each measurement.
X_train = X_train[,extract_features]


# 12. Load activity data
y_train[,2] = activity_labels[y_train[,1]]
names(y_train) = c("Activity_ID", "Activity_Label")
names(subject_train) = "subject"


# 13. Bind data
train_data <- cbind(as.data.table(subject_train), y_train, X_train)


# 14. Merge test and train data
data = rbind(test_data, train_data)

id_labels   = c("subject", "Activity_ID", "Activity_Label")
data_labels = setdiff(colnames(data), id_labels)
melt_data      = melt(data, id = id_labels, measure.vars = data_labels)


# 15. Apply mean function to dataset using dcast function
my_tidy_data   = dcast(melt_data, subject + Activity_Label ~ variable, mean)

# 16. write.table(my_tidy_data, file = "./tidy_data.txt")
