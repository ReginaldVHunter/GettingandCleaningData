# GettingandCleaningData

##Run analysis##

#Give the file URL#
URLzip?"https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"

#create temp file#
tmp<-tempfile()  
#download the URL file to temp file#
download.file(URLzip,tmp) 

#read data from the zip files#
traindata<-read.table(unz(tmp,"UCI HAR Dataset/train/X_train.txt"))
testdata<-read.table(unz(tmp,"UCI HAR Dataset/test/X_test.txt"))
testlabels<-read.table(unz(tmp,"UCI HAR Dataset/test/y_test.txt"))
trainlabels<-read.table(unz(tmp,"UCI HAR Dataset/train/y_train.txt"))
features<-read.table(unz(tmp,"UCI HAR Dataset/features.txt"))
subjects_test<-read.table(unz(tmp,"UCI HAR Dataset/test/subject_test.txt"))
subjects_train<-read.table(unz(tmp,"UCI HAR Dataset/train/subject_train.txt"))

unlink(tmp) #unlink the temp file#


#combine the multiple test and training tables#
combined<-rbind(testdata,traindata)
combinedlabels<-rbind(testlabels,trainlabels)
combinedsubjects<-rbind(subjects_test,subjects_train)
#rename columns#
names(combinedsubjects)[names(combinedsubjects)=='V1']<-'Subjects'
names(combinedlabels)[names(combinedlabels)=='V1']<-'Activity'
#rename columns from the features list#
colnames(combined)<-features$V2

#provide the mean and std extracts#
means_stds<-combined[,grep('mean()|std()', names(combined), value=TRUE)]

#use codes to rename the activity codes#
combinedlabels$Activity[combinedlabels$Activity==1]<-"Walking"
combinedlabels$Activity[combinedlabels$Activity==2]<-"Walking upstairs"
combinedlabels$Activity[combinedlabels$Activity==3]<-"Walking downstairs"
combinedlabels$Activity[combinedlabels$Activity==4]<-"Sitting"
combinedlabels$Activity[combinedlabels$Activity==5]<-"Standing"
combinedlabels$Activity[combinedlabels$Activity==6]<-"Laying"

#provide the combined tables#
combined_Val_labels<-cbind(combinedlabels,combinedsubjects,means_stds)
library(data.table) #load data.table package
summary_results<-as.data.table(combined_Val_labels)
subject_activity<-summary_results[,lapply(.SD,mean),by=list(Activity,Subjects)]

