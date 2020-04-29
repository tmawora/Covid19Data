# Covid19Data
This extracts daily data from the provided website. It further merges data by country, looking at overall country cases.

# Downloading data 

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

Happy coding
