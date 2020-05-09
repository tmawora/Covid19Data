## Covid-19 Data
This extracts daily data from the provided website. It further merges data by country, looking at overall country cases.

## Downloading data 

All data will be downloaded from the following website: 'https://raw.githubusercontent.com/CSSEGISandData/COVID-19/master/csse_covid_19_data/csse_covid_19_daily_reports/01-22-2020.csv'. 

The owner of the site keeps track of daily record of Covid-19 cases. I am grateful to the team for their consistency. 

From the link, notice that the data are named according to the date. For instance, to get data for the next day, just change the date to "01-23-2020". Thus, our task is to keep changing the date in the link. I tried the site and found that the data starts from 22nd January 2020. 

Codes in this section download the data to yesterday and saves them in a specified directory. Data for each day are saved in different `.csv` files that are named according to the date.

To work with dates, we will use the `lubridate` library and functions therein. See the codes below. In case you don't have it in your computer, then install it first using the `install.packages("lubridate")` command.

```
library(lubridate)
for(i in 0:as.numeric(today()-mdy("01-22-2020")-1)){
  datet<-format(mdy("01-22-2020")+i,"%m-%d-%Y")
  datet<-as.character(datet)
  url <- paste("https://raw.githubusercontent.com/CSSEGISandData/COVID-19/master/csse_covid_19_data/csse_covid_19_daily_reports/",datet,".csv",sep="")
  destfile <- paste("C:/Users/TJIJD/Dropbox/Projects/2020/covid-19/",datet,".csv",sep="")
  download.file(url, destfile)
}
```

From the above codes, `destfile` is the destination file. The first part of the file contains the path to the directory. You should modify it to a path within your computer.

Run the code to get the data files. They will be different files. Open a few and study them so you can see how to merge them.

## Updating data from date of last collection to current

The previous codes get started with the data. In case you already have some of the data, then you don't need to redownload all of them. You can easily start from the last time you saved. 

To do this, first get the working directory, it is prefferable you set it up using the codes below. Notice that the link leads to a directory in my computer. Edit it to the directory in which you stored the previous data.

```
setwd("C:/Users/TJIJD/Dropbox/Projects/2020/covid-19/RawData/")
```
Thereafter, we will select the date from of the last entry. The last date is the last value in the `file` which is the object storing the names of the datasets in the directory.

```
library(lubridate)
files<-list.files()
files<-files[length(files)]
files<-substr(files[1],1,10)
```

Finally, a small edit to the command to be able to collect the data starting from the day after date of last stop.
```
for(i in 1:as.numeric(today()-mdy(files))){
  datet<-format(mdy(files)+i,"%m-%d-%Y")
  datet<-as.character(datet)
  url <- paste("https://raw.githubusercontent.com/CSSEGISandData/COVID-19/master/csse_covid_19_data/csse_covid_19_daily_reports/",datet,".csv",sep="")
  destfile <- paste("C:/Users/TJIJD/Dropbox/Projects/2020/covid-19/RawData/",datet,".csv",sep="")
  download.file(url, destfile)
}
```
## Merging the data

It is imperative we merge the multiple datasets so we can work with a single file. This will enable one to extract the section he/she wants to use , by country or date.

To make the codes simpler, we will set the working directry for the purpose of selecting the data as we need them. We will further read data from the first daye (22nd Janyaru 2020).

```
setwd("C:/Users/TJIJD/Dropbox/Projects/2020/covid-19/RawData/")
files<-list.files()
newData<-read.csv(files[1],header=TRUE,stringsAsFactors = FALSE)
```
It is important to ensure that the option `stringsAsFactors` is set to `FALSE` as we will later use the data for extracting date values. 

The names of the data might change, but there are key words. I checked how the names had changed over time. 
The funtion `procnames` will help us maintain the same names for all datasets.

```
naming<-names(newData)

procnames<-function(naming){
  naming[grep("FIPS",naming)]<-"FIPS"
  naming[grep("Province",naming)]<-"Province"
  naming[grep("Country",naming)]<-"Country"
  naming[grep("Last",naming)]<-"Last_Update"
  naming[grep("Lat",naming)]<-"Lat"
  naming[grep("Long",naming)]<-"Long"
  naming[grep("Confirmed",naming)]<-"Confirmed"
  naming[grep("Death",naming)]<-"Deaths"
  naming[grep("Recovered",naming)]<-"Recovered"
  naming[grep("Active",naming)]<-"Active"
  naming[grep("Combined_Key",naming)]<-"Combined_Key"
  naming[grep("Admin",naming)]<-"Admin"
  return(naming)
}
```
We therefore will keep on changing the column names using the `procnames` defined above.  For instance, to change the names for the first file we will apply as shown below.

```
naming<-procnames(naming)
names(newData)<-naming
```
Finally, we will call the library `plyr` that has the function `rdbind.fill` which appends data, and includes all columns from the two datasets.
To merge, we will loop starting from the second dataset to the last one in the directory using the codes below.
```
library(plyr)
for(i in 2:length(files)){
  newData2<-read.csv(files[i],header=TRUE,stringsAsFactors = FALSE)
  naming<-names(newData2)
  naming<-procnames(naming)
  names(newData2)<-naming
  newData<-rbind.fill(newData,newData2)
}
```
I will be interested in a few columns, which include the country as the administrative boundary, and not the FIPS and province. 
```
newData<-newData[,c(2:8,11)]
```

One can now edit and subset the rows of interest. For instance, for my next codes, I will focus on my country, Kenya, and its neighbours.

Finally, one can save the file using the `write.csv` function as below.

```
write.csv(newData,"Covid19.csv")
```

Happy coding
