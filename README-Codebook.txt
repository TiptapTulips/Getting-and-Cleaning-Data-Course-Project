
#Reading files
testST <- read.table("test/subject_test.txt")
trainST <- read.table("train/subject_train.txt")


xtrain = read.table("train/X_train.txt")
ytrain = read.table("train/y_train.txt")
subject_train = read.table("train/subject_train.txt")

xtest = read.table("test/X_test.txt")
ytest = read.table("test/y_test.txt")
subject_test = read.table("test/subject_test.txt")

features = read.table("features.txt")
activityLabels = read.table("activity_labels.txt")


#Creating sanity and column values
colnames(xtrain) = features[,2]
colnames(ytrain) = "activityId"
colnames(subject_train) = "subjectId"

colnames(xtest) = features[,2]
colnames(ytest) = "activityId"
colnames(subject_test) = "subjectId"

colnames(activityLabels) <- c('activityId','activityType')

#Merging
mrg_train = cbind(ytrain, subject_train, xtrain)
mrg_test = cbind(ytest, subject_test, xtest)

setAllInOne = rbind(mrg_train, mrg_test)


colNames = colnames(setAllInOne)
mean_and_std = (grepl("activityId" , colNames) | grepl("subjectId" , colNames) | grepl("mean.." , colNames) | grepl("std.." , colNames))

setForMeanAndStd <- setAllInOne[ , mean_and_std == TRUE]


setWithActivityNames = merge(setForMeanAndStd, activityLabels, by='activityId', all.x=TRUE)

secTidySet <- aggregate(. ~subjectId + activityId, setWithActivityNames, mean)
secTidySet <- secTidySet[order(secTidySet$subjectId, secTidySet$activityId),]

write.table(secTidySet, "secTidySet.txt", row.name=FALSE)

