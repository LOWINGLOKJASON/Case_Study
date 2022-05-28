### **Case Study: How Does a Bike-Share Navigate Speedy Success?**

![Bike](https://user-images.githubusercontent.com/100983196/170842811-3ee7c378-f742-45a8-bc41-8fd709989e9f.png)

**Introduction**

This is one of the case studies in the [Google Data Analytics - Professional Certificate](https://www.coursera.org/professional-certificates/google-data-analytics?utm_source=gg&utm_medium=sem&utm_campaign=15-GoogleDataAnalytics-ROW&utm_content=B2C&campaignid=12566515400&adgroupid=117869292845&device=c&keyword=google%20certification%20data%20analytics&matchtype=b&network=g&devicemodel=&adpostion=&creativeid=507290840621&hide_mobile_promo&gclid=CjwKCAjwkMeUBhBuEiwA4hpqELQ0rcYSB6l69SJQR8Yqi9xMsMrAEYQ1-5Ja5TFIEhEIaTlMnmNERhoC8L8QAvD_BwE).

**ASK**

_Background_
Cyclistic is a bike-sharing company in Chicago since 2016. It offers more than 5,800 bicycles, 600 docking stations and various bike types such as reclining bikes, hand tricycles and cargo bikes. The majority of riders opt for traditional bikes; about 8% of riders use the assistive options. Cyclistic users are more likely to ride for leisure, but about 30% use them to commute to work each day

_Scenario_
As a junior data analyst in the marketing analyst team at Cyclistic, knowing that the company had an approach that can build general awareness and appeal to broad consumer segments was the flexibility of its pricing plans: single-ride passes, full-day passes, and annual memberships.

_Business Task_
Maximizing the number of annual members by converting casual riders into annual members

_Project Objective_
How do annual members and casual riders use Cyclistic bikes differently?

_Key Stakeholders_
_Lily Moreno_: The director of marketing and your manager who is responsible for the development of campaigns and initiatives to promote the bike-share program. These may include email, social media, and other channels.
_Cyclistic marketing analytics team_: A team of data analysts who are responsible for collecting, analyzing, and reporting data that helps guide Cyclistic marketing strategy.
_Cyclistic executive team_: The notoriously detail-oriented executive team will decide whether to approve the recommended marketing program.

**PREPARE**

The case study will use Cyclistic’s historical trip data
- Excel: [the previous 12 months of Cyclistic trip data (May/2021-Apr/2022)](https://divvy-tripdata.s3.amazonaws.com/index.html)
- RStudio: [the previous 4 quarters of Cyclistic trip data (Q2/2019-Q1/2020)](https://divvy-tripdata.s3.amazonaws.com/index.html)
(The datasets have a different name because Cyclistic is a fictional company. For the purposes of this case study, the datasets are appropriate and will enable you to answer the business questions. The data has been made available by Motivate International Inc. under this [license](https://ride.divvybikes.com/data-license-agreement))

**PROCESS**

_Excel_

1. download and unzip the folders, extract the CSV files into a folder
2. copy the files into XSL files
3. add a column "ride_length" with the formula `=D2-C2` below it and drag it to the last row
4. format the whole column as HH:MM:SS using Format > Cells > Time > 37:30:55
5. add a column "day_of_week" with the formula `=WEEKDAY(C2,1)` (assuming 1=Sunday, 7=Saturday) below it and drag it to the last row
6. remove the entire row where "start_station_name" or "start_station_name" column is blank
7. filter and select only “#######” shown in ride_length column as it indicates the ride time is negative

_RStudio_

1. load packages
```
library(tidyverse) 
library(lubridate)  
library(ggplot2)  
```
2. displays working directory
`getwd() `

3. sets the working directory to simplify calls to data
`setwd("C:/Users/Document") `

4. upload Divvy datasets
```
q2_2019 <- read_csv("Divvy_Trips_2019_Q2.csv")
q3_2019 <- read_csv("Divvy_Trips_2019_Q3.csv")
q4_2019 <- read_csv("Divvy_Trips_2019_Q4.csv")
q1_2020 <- read_csv("Divvy_Trips_2020_Q1.csv")

```
5. compare column names each of the files
```
colnames(q2_2019)
colnames(q3_2019)
colnames(q4_2019)
colnames(q1_2020)
```
- as the column names are not identical among the 4 quarters
6. rename the column names as same as 2020_Q1
```
(q2_2019 <- rename(q2_2019
                   ,ride_id = "01 - Rental Details Rental ID"
                   ,rideable_type = "01 - Rental Details Bike ID" 
                   ,started_at = "01 - Rental Details Local Start Time"  
                   ,ended_at = "01 - Rental Details Local End Time"  
                   ,start_station_name = "03 - Rental Start Station Name" 
                   ,start_station_id = "03 - Rental Start Station ID"
                   ,end_station_name = "02 - Rental End Station Name" 
                   ,end_station_id = "02 - Rental End Station ID"
                   ,member_casual = "User Type"))
(q3_2019 <- rename(q3_2019
                   ,ride_id = "trip_id"
                   ,rideable_type = "bikeid" 
                   ,started_at = "start_time"  
                   ,ended_at = "end_time"  
                   ,start_station_name = "from_station_name" 
                   ,start_station_id = "from_station_id" 
                   ,end_station_name = "to_station_name" 
                   ,end_station_id = "to_station_id" 
                   ,member_casual = "usertype"))
(q4_2019 <- rename(q4_2019
                   ,ride_id = "trip_id"
                   ,rideable_type = "bikeid" 
                   ,started_at = "start_time"  
                   ,ended_at = "end_time"  
                   ,start_station_name = "from_station_name" 
                   ,start_station_id = "from_station_id" 
                   ,end_station_name = "to_station_name" 
                   ,end_station_id = "to_station_id" 
                   ,member_casual = "usertype"))
```
7. check the consistency of data frames
```
str(q2_2019)
str(q3_2019)
str(q4_2019)
str(q1_2020)
```
8. revise the data type to character in columns to be consistent as 2020_Q1
```
q2_2019 <-  mutate(q2_2019, ride_id = as.character(ride_id)
                   ,rideable_type = as.character(rideable_type)) 
q3_2019 <-  mutate(q3_2019, ride_id = as.character(ride_id)
                   ,rideable_type = as.character(rideable_type)) 
q4_2019 <-  mutate(q4_2019, ride_id = as.character(ride_id)
                   ,rideable_type = as.character(rideable_type)) 
```
9. combine four quarters' data frames into one data frame
`all_trips <- bind_rows(q2_2019, q3_2019, q4_2019, q1_2020)`

10. remove lat, long, birthyear, and gender fields
```
all_trips <- all_trips %>%  
  select(-c(start_lat, start_lng, end_lat, end_lng, birthyear, gender, 
            "01 - Rental Details Duration In Seconds Uncapped", 
            "05 - Member Details Member Birthday Year", 
            "Member Gender", 
            "tripduration"))
```
11. check the properties of the new table
- list of column names
`colnames(all_trips)`
- total number of rows
`nrow(all_trips)`
- dimensions of data frame
`dim(all_trips)`
- first 6 rows of data frame
`head(all_trips)`
- last 6 rows of data frame
`tail(all_trips)`
- list of columns and data types
`str(all_trips)`
- statistical summary of data
`summary(all_trips)`

12. uniform the data labels
- replace "Subscriber" with "member" and "Customer" with "casual"
- observe the overall result in the member_casual column with 4 labels
`table(all_trips$member_casual)`
- reassign the labels to the desired values
```
all_trips <-  all_trips %>% 
  mutate(member_casual = recode(member_casual
                                ,"Subscriber" = "member"
                                ,"Customer" = "casual"))
```
- double-check if the member_casual column results in only two sets of data
`table(all_trips$member_casual)`

13. add columns that list the date, month, day, and year of each ride
- aggregate ride data for each month, day, or year
- the default date format is yyyy-mm-dd
```
all_trips$date <- as.Date(all_trips$started_at) 
all_trips$year <- format(as.Date(all_trips$date), "%Y")
all_trips$month <- format(as.Date(all_trips$date), "%m")
all_trips$day <- format(as.Date(all_trips$date), "%d")
all_trips$day_of_week <- format(as.Date(all_trips$date), "%A")
```
14. add a ride_length calculation to all_trips (in seconds)
`all_trips$ride_length <- difftime(all_trips$ended_at,all_trips$started_at)`

15 check the columns' structure
`str(all_trips)
`
16. convert ride_length from Factor to numeric to run calculations on the data
```
is.factor(all_trips$ride_length)
all_trips$ride_length <- as.numeric(as.character(all_trips$ride_length))
is.numeric(all_trips$ride_length)
```
17. remove dirty data
- the data frame includes some entries when bikes were taken out of docks and checked for quality by Divvy or ride_length was negative
- create a new version of the dataframe since data is being removed
`all_trips_v2 <- all_trips[!(all_trips$start_station_name == "HQ QR" | all_trips$ride_length<0),]`

**ANALYZE**

_Excel_

_Calcaulations_

- Calculate the mean of ride_length by using the formula `=AVERAGE()`, the range within the parenthesis should be the entire ride_length, format it as HH:MM:SS using Format > Cells > Time > 37:30:55
- Calculate the max ride_length by using the formula `=MAX()`, the range within the parenthesis should be the entire ride_length, format it as HH:MM:SS using Format > Cells > Time > 37:30:55
- Calculate the mode of day_of_week by using the formula `=MODE()`, the range within the parenthesis should be the entire day_of_week

_Pivot Table_

- Calculate the average ride_length for members and casual riders. Try rows = member_casual; Values = Average of ride_length.
- Calculate the average ride_length for users by day_of_week. Try columns = day_of_week; Rows = member_casual; Values = Average of ride_length.
- Calculate the number of rides for users by day_of_week by adding Count of ride_id to Values.

_RStudio_

1. conduct a descriptive analysis on ride_length (all figures in seconds)_
- average riding duration (total ride length / rides)
`mean(all_trips_v2$ride_length) `
- midpoint number in the ascending array of ride lengths
`median(all_trips_v2$ride_length) `
- longest ride
`max(all_trips_v2$ride_length) `
- shortest ride
`min(all_trips_v2$ride_length) `
- summary of ride_length
`summary(all_trips_v2$ride_length)`

2. comparison amid members and casual riders_
```
aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual, FUN = mean)
aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual, FUN = median)
aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual, FUN = max)
aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual, FUN = min)
```
3. find the average ride time by each day for members vs casual users
`aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual + all_trips_v2$day_of_week, FUN = mean)`
- notice that the days of the week are out of order

4. rearrange the order of days of week
`all_trips_v2$day_of_week <- ordered(all_trips_v2$day_of_week, levels=c("Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"))`

5. check the average ride time by each day for members and casual users
`aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual + all_trips_v2$day_of_week, FUN = mean)`

6. integrate a formula with the following
- analyze ridership data by type and weekday
- create a weekday field
- group by user type and weekday
- calculates the number of rides and average duration
- calculate the average duration
- sort
```
all_trips_v2 %>% 
  mutate(weekday = wday(started_at, label = TRUE)) %>%  
  group_by(member_casual, weekday) %>%  #
  summarise(number_of_rides = n()                             
            ,average_duration = mean(ride_length)) %>%         
  arrange(member_casual, weekday)
```

**SHARE** 

_Excel_

_The overview of riders in Cyclistic_
<html xmlns:o="urn:schemas-microsoft-com:office:office"
xmlns:x="urn:schemas-microsoft-com:office:excel"
xmlns="http://www.w3.org/TR/REC-html40">

<head>

<meta name=ProgId content=Excel.Sheet>
<meta name=Generator content="Microsoft Excel 15">
<link id=Main-File rel=Main-File
href="file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip.htm">
<link rel=File-List
href="file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_filelist.xml">

<!--table
	{mso-displayed-decimal-separator:"\.";
	mso-displayed-thousand-separator:"\,";}
@page
	{margin:.75in .7in .75in .7in;
	mso-header-margin:.3in;
	mso-footer-margin:.3in;}
tr
	{mso-height-source:auto;}
col
	{mso-width-source:auto;}
br
	{mso-data-placement:same-cell;}
td
	{padding-top:1px;
	padding-right:1px;
	padding-left:1px;
	mso-ignore:padding;
	color:black;
	font-size:11.0pt;
	font-weight:400;
	font-style:normal;
	text-decoration:none;
	font-family:Calibri, sans-serif;
	mso-font-charset:0;
	mso-number-format:General;
	text-align:general;
	vertical-align:bottom;
	border:none;
	mso-background-source:auto;
	mso-pattern:auto;
	mso-protection:locked visible;
	white-space:nowrap;
	mso-rotate:0;}
.xl63
	{mso-number-format:"\[h\]\:mm\:ss\;\@";}
.xl64
	{mso-number-format:"mmm\\-yy\;\@";}
.xl65
	{mso-number-format:"mmm\\-yy\;\@";
	text-align:center;}
.xl66
	{mso-number-format:"\[h\]\:mm\:ss\;\@";
	text-align:center;}
.xl67
	{text-align:center;}
.xl68
	{color:white;
	font-weight:700;
	mso-number-format:"mmm\\-yy\;\@";
	text-align:center;
	border-top:1.0pt solid windowtext;
	border-right:none;
	border-bottom:none;
	border-left:none;
	background:#FFC000;
	mso-pattern:black none;}
.xl69
	{color:white;
	font-weight:700;
	mso-number-format:"\[h\]\:mm\:ss\;\@";
	text-align:center;
	border-top:1.0pt solid windowtext;
	border-right:none;
	border-bottom:none;
	border-left:none;
	background:#FFC000;
	mso-pattern:black none;}
.xl70
	{color:white;
	font-weight:700;
	text-align:center;
	border-top:1.0pt solid windowtext;
	border-right:none;
	border-bottom:none;
	border-left:none;
	background:#FFC000;
	mso-pattern:black none;}
-->

</head>

<body link="#0563C1" vlink="#954F72">



Month | mean ride_length | max ride_length | mode day_of_week
-- | -- | -- | --
May-21 | 0:26:30 | 898:41:36 | 7
Jun-21 | 0:26:19 | 932:24:09 | 7
Jul-21 | 0:24:12 | 818:27:09 | 7
Aug-21 | 0:21:09 | 693:49:10 | 1
Sep-21 | 0:20:11 | 547:38:32 | 7
Oct-21 | 0:20:08 | 678:25:01 | 7
Nov-21 | 0:14:06 | 371:19:44 | 3
Dec-21 | 0:14:14 | 506:40:33 | 5
Jan-22 | 0:12:57 | 487:51:06 | 5
Feb-22 | 0:13:03 | 181:45:58 | 2
Mar-22 | 0:16:58 | 572:34:04 | 4
Apr-22 | 0:16:27 | 125:45:58 | 7
Overall | 0:18:51 | 932:24:09 | 7



</body>

</html>

_RStudio_

- the average ride length from May 2021 to April 2022 was 18 minutes and 51 seconds
- the longest ride length was on June 2021, 932 hours, 24 minutes and 9 seconds
- the mode of the day of the week is 7, meaning that most people used share bike service on Saturday

_Overall usage_

![Image](https://user-images.githubusercontent.com/100983196/170840425-7075adc5-a65f-4d7d-86c4-c2928ff10026.png)

- in the past 12 months (May 2021- Apr 2022), there were 4.6 million (4,660,680) people used Cyclistic bike-sharing service
- annual members in Cyclistic took a greater proportion of bike-sharing service, composing 56% of the usage
- casual riders accounted for the remaining 44% of the total usage

_Average riding duration_

![Image](https://user-images.githubusercontent.com/100983196/170840518-cc95848d-b52b-458e-a4aa-28ae8bac8329.png)

- annual members utilized the bike-sharing service in a uniform time length, standing between 7 and 14 hours.
- the casual riders utilized more riding time than the Cyclistic members, at least double the riding time of the members

_Average riding duration by day of week_

![Image](https://user-images.githubusercontent.com/100983196/170840569-9d5e51f1-0277-428c-9e3f-a0b96c2c8878.png)

- u-shaped diagram from Sunday to Saturday indicates that both casual riders and members had longer bike using time at the weekends
- there was a more noticeable disparity in average ride length on weekdays and weekends for casual riders

_Number of rides by day of week_

![Image](https://user-images.githubusercontent.com/100983196/170840695-3fa4285f-6a97-4b45-87fd-b73deb554555.png)

- the number of casual riders at weekends was higher than on weekdays
- in contrast, the members rode bikes on the weekdays are more than at weekends 

_RStudio_

_Average duration by rider type_

`counts <- aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual + all_trips_v2$day_of_week, FUN = mean)`


<html xmlns:o="urn:schemas-microsoft-com:office:office"
xmlns:x="urn:schemas-microsoft-com:office:excel"
xmlns="http://www.w3.org/TR/REC-html40">

<head>

<meta name=ProgId content=Excel.Sheet>
<meta name=Generator content="Microsoft Excel 15">
<link id=Main-File rel=Main-File
href="file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip.htm">
<link rel=File-List
href="file:///C:/Users/USER/AppData/Local/Temp/msohtmlclip1/01/clip_filelist.xml">

<!--table
	{mso-displayed-decimal-separator:"\.";
	mso-displayed-thousand-separator:"\,";}
@page
	{margin:.75in .7in .75in .7in;
	mso-header-margin:.3in;
	mso-footer-margin:.3in;}
tr
	{mso-height-source:auto;}
col
	{mso-width-source:auto;}
br
	{mso-data-placement:same-cell;}
td
	{padding-top:1px;
	padding-right:1px;
	padding-left:1px;
	mso-ignore:padding;
	color:black;
	font-size:11.0pt;
	font-weight:400;
	font-style:normal;
	text-decoration:none;
	font-family:Calibri, sans-serif;
	mso-font-charset:0;
	mso-number-format:General;
	text-align:general;
	vertical-align:bottom;
	border:none;
	mso-background-source:auto;
	mso-pattern:auto;
	mso-protection:locked visible;
	white-space:nowrap;
	mso-rotate:0;}
-->

</head>

<body link="#0563C1" vlink="#954F72">



  | all_trips_v2$member_casual | all_trips_v2$day_of_week | all_trips_v2$ride_length
-- | -- | -- | --
1 | casual | Sunday | 3581.405
2 | member | Sunday | 919.9746
3 | casual | Monday | 3372.287
4 | member | Monday | 842.5726
5 | casual | Tuesday | 3596.36
6 | member | Tuesday | 826.1427
7 | casual | Wednesday | 3718.662
8 | member | Wednesday | 823.9996
9 | casual | Thursday | 3682.985
10 | member | Thursday | 823.9278
11 | casual | Friday | 3773.835
12 | member | Friday | 824.5305
13 | casual | Saturday | 3331.914
14 | member | Saturday | 968.9337



</body>

</html>

```
all_trips_v2 %>% 
  mutate(weekday = wday(started_at, label = TRUE)) %>% 
  group_by(member_casual, weekday) %>% 
  summarise(number_of_rides = n()
            ,average_duration = mean(ride_length)) %>% 
  arrange(member_casual, weekday)  %>% 
  ggplot(aes(x = weekday, y = average_duration, fill = member_casual)) +
  geom_col(position = "dodge")

```
![Image](https://user-images.githubusercontent.com/100983196/170840798-ded579fe-6472-4758-817c-af6407ce9aa0.png)

- from Q2 2019 to Q1 2020, the average duration for casual riders tripled the average duration for members
- members kept the average duration low from Sunday to Saturday
- but the average duration for both riders stayed evenly at all time

_the number of rides by rider type_

```
all_trips_v2 %>% 
  mutate(weekday = wday(started_at, label = TRUE)) %>% 
  group_by(member_casual, weekday) %>% 
  summarise(number_of_rides = n()
            ,average_duration = mean(ride_length)) %>% 
  arrange(member_casual, weekday)  %>% 
  ggplot(aes(x = weekday, y = number_of_rides, fill = member_casual)) +
  geom_col(position = "dodge")
```
![Image](https://user-images.githubusercontent.com/100983196/170841000-85fa8fed-ce3b-4d52-a346-99272270f19f.png)

- the majority of members rode bikes on weekdays while most of the casual riders used bike-sharing services at weekends
- the overall number of members is more than the number of casual riders at all time 

**ACT**

_1. Plan regulations_

_Single ride passes_: users can ride bikes for a single limited time, the bikes' pick up and return locations are limited and it’s only available at weekends

_Full-day passes_: users can ride bikes at any time within the day, the bikes' pick up and return locations are limited and the availability is only on weekdays

_Members_: annual members can use the bikes anytime per year and they have full access to a pick and return the bikes in all locations

- As the ride length of casual riders is higher than that of members, adjusting the plan regulations can turn casual riders into members

During the weekends,
- most people ride bikes for leisure, especially on Saturday and Sunday
- the number of casual riders at weekends was higher on weekdays
- users prefer an unlimited riding time to spend in a whole day
- go to some popular landscapes for sightseeing in different locations
- membership can provide an endless ride length which members can pick up and return bikes at all bike stations

During the weekdays,
- users usually ride bikes to commute on the weekdays
- users may choose either full day or upgrade as members to have more choices on locations 

_2. Price adjustment_

_Revise the price for casual and member riders_

the average fee of the single ride passes should be the highest, the full-day passes should have a lower average price than the single passes, and members should have the lowest average price

During the weekends,
- it may attract the leisure riders at weekend change into members as they can enjoy lower average cost for long riding duration compared to single ride passes

During the weekdays,
- commuters will need to pay less on average if choosing membership instead of full-day passes on weekdays
- members will acquire low-cost benefits to commute by share-biking in the long run

_3. Free or discounted trial_

_Casual riders can have a free or discounted trial on membership for a certain period like 1 week to experience share-bike as members_

- the method to gain the free or discounted trial is to interact with the Cyclistic social media pages
- for instance, comment, like, subscribe and share the company’s social media pages and acquire the free or discounted trial
- win-win situation: casual riders can enjoy the reward whereas the company can have the advertisement with a lower operating cost
- hosting members-only activities can also be an incentive for casual riders joining the membership 

