# getting-and-cleaning-data
assignment of getting and cleaning data course


##Getting and cleaning data assignment-week4


##1) Exploring files, checking up dimentions ############################################################

## 1.1) Subjects
subject_train <- read.table("./subject_train.txt",sep=",",header=TRUE)
dim(subject_train) 
unique<-unique(subject_train)
class(unique)
unique<-unique[,1]
length(unique)
## vector of length 7351, lists individuals 
## observations on 21 participants
subject_test <- read.table("./subject_test.txt",sep=",",header=TRUE)
dim(subject_test) 
str(subject_test)
unique<-unique(subject_test)
class(unique)
unique<-unique[,1]
length(unique)
## vector of length 2946, lists individuals 
## observations on 9 participants

## 1.2) X_train
X_train <- read.table("./X_train.txt",sep="") ##,header=TRUE)
head(X_train)
str(X_train)
## 7352 obs. of  561 variables
X_test <- read.table("./X_test.txt",sep="") ##,header=TRUE)
head(X_test)
str(X_test)
## 2947 obs. of  561 variables


y_train <- read.table("./y_train.txt",sep="",header=TRUE)
y_train
dim(y_train) ##7351 1
unique(y_train)
## lists activities (1 to 6)
y_test <- read.table("./y_test.txt",sep="",header=TRUE)
y_test
dim(y_test) ##2946 1
unique(y_test)

##2) merge the corresponding train and test sets 
########################################

##2.1) merging subjects
colnames(subject_train) <- c("subject")
colnames(subject_test) <- c("subject")
subjects <-rbind(subject_train, subject_test)
dim(subjects) ## 10297 observations
unique <-unique(subjects) 
length(unique[,1]) ## 30 parrticipants
head(subjects)
tail(subjects)
subjects <- subjects[order(subjects$subject),]
length(subjects) 
## 10297

##2.2) merging X

X <-rbind(X_train, X_test)
str(X)
dim(X) ##  10299   561
sum(is.na(X)==TRUE)


##2.3) merging y
y <-rbind(y_train, y_test)
dim(y) ## 10297     1


##3) Extract measurements on mean and standard deviation
#################################################

features <- read.table("features.txt")
str(features)
m_sd_features <-grep("[Mm]ean|[Ss]td", features[,2]) ##extracts mean and sd
features[m_sd_features,2]
length(m_sd_features) ## 86
X <- X[, m_sd_features]
dim(X) ##  10299    86
names(X)<-features[m_sd_features,2]
head(X)
unique(colnames(X))

##4) Name activities
###############################################

activities <- read.table("activity_labels.txt")
dim(activities)
names(y)<-c("activity")
y[, 1] <- activities[y[, 1], 2]
head(y)

##5) merge everything together
##############################################

str(subjects)
str(y)
str(X)
length(X[,1])
l <-length(X[,1]) - 2
X<-X[1:l,]
dim(X)
data <-cbind(subjects, y, X)
head(data, n=2)
save(data,file="data.Rda")


##6) Create a subset containing avarages of each variable per activity per subject
##################################################################################

install.packages("reshape2")
library("reshape2")

##6.1) factorizing subjects and activities

data$subject <-as.factor(data$subject)
data$activity <-as.factor(data$activity)

##6.2) computing averages and saving

data.melted <-melt(data, id=c("subject","activity"))
data.mean <- dcast(data.melted, subject + activity ~ variable, mean)
dim(data.mean)

save(data.mean,file="data.mean.Rda")










