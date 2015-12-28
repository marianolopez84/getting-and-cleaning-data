# getting-and-cleaning-data

filename <- "C:/Users/210062828/Desktop/Data Science/UCI HAR Dataset" #data downloaded and unzipped in working directory

library(reshape2)

#obtain heads and features from provided files
heads <- read.table("C:/Users/210062828/Desktop/Data Science/UCI HAR Dataset/activity_labels.txt")
heads[,2] <- as.character(heads[,2])
features <- read.table("C:/Users/210062828/Desktop/Data Science/UCI HAR Dataset/features.txt")
features[,2] <- as.character(features[,2]) 

#mean and standard deviation
measurements <- grep(".*mean.*|.*std.*", features[,2])
measurements.names <- features[measurements,2]
measurements.names = gsub('-mean', 'Mean', measurements.names)
measurements.names = gsub('-std', 'Std', measurements.names)
measurements.names <- gsub('[-()]', '', measurements.names)

test <- read.table("C:/Users/210062828/Desktop/Data Science/UCI HAR Dataset/test/X_test.txt")[measurements]
testActivities <- read.table("C:/Users/210062828/Desktop/Data Science/UCI HAR Dataset/test/Y_test.txt")
testSubjects <- read.table("C:/Users/210062828/Desktop/Data Science/UCI HAR Dataset/test/subject_test.txt")
test <- cbind(testSubjects, testActivities, test)

train <- read.table("C:/Users/210062828/Desktop/Data Science/UCI HAR Dataset/train/X_train.txt")[measurements]
trainActivities <- read.table("C:/Users/210062828/Desktop/Data Science/UCI HAR Dataset/train/Y_train.txt")
trainSubjects <- read.table("C:/Users/210062828/Desktop/Data Science/UCI HAR Dataset/train/subject_train.txt")
train <- cbind(trainSubjects, trainActivities, train)


# merge test and train datasets and add labels
allData <- rbind(train, test)
colnames(allData) <- c("subject", "activity", measurements.names)


allData$activity <- factor(allData$activity, levels = heads[,1], labels = heads[,2])
allData$subject <- as.factor(allData$subject)

allData.melted <- melt(allData, id = c("subject", "activity"))
allData.mean <- dcast(allData.melted, subject + activity ~ variable, mean)

write.table(allData.mean, "tidy.txt", row.names = FALSE, quote = FALSE)

