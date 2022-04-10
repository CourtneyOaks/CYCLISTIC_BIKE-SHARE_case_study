# CYCLISTIC BIKE-SHARE case study

Courtney Oaks, 02/27/2022, Data Analytics Professional Certification capstone project

### About the company

Cyclistic is a (fictional) bike-share company located in Chicago. Cyclistic sets itself apart by also offering reclining bikes, hand tricycles, and cargo bikes, making bike-share more inclusive to people with disabilities and riders who can't use a standard two-wheeled bike. The majority of riders opt for traditional bikes; about 8% of riders use assistive options. Cyclistic users are more likely to ride for leisure, but about 30% use them to commute to work each day. Until now, Cyclistic’s marketing strategy relied on building general awareness and appealing to broad consumer segments. One approach that helped make these things possible was the flexibility of its pricing plans: single-ride passes, full-day passes, and annual memberships. Customers who purchase single-ride or full-day passes are referred to as casual riders. Customers who purchase annual memberships are Cyclistic members.

### Scenario

You are a junior data analyst working in the marketing analyst team at Cyclistic. The director of marketing believes the company's future success depends on maximizing the number of annual members. Your team wants to understand how casual riders and annual members use Cyclistic bikes differently. From your insights, your team will design a new marketing strategy to convert casual riders into annual members. 

### Stakeholders

- Cyclistic: A (fictional) bike-share company that features more than 5,800 bicycles and 600 docking stations.

- Lily Moreno: The director of marketing and your manager. Moreno is responsible for the development of campaigns and initiatives to promote the bike-share program. These may include email, social media, and other channels. 

- Cyclistic marking analytics team: A team of data analysts who are responsible for collecting, analyzing, and reporting data that helps guide Cyclistic's marketing strategy.

- Cyclistc executive team: The notoriously detail-oriented executive team will decide whether to approve the recommended marketing program.

### Business task

Use ride data from the last 12 months to come up with a strategy for the marketing team to help convert casual riders into annual members. Use the data to provide insights into differences between casual riders and annual members and give recommendations. 

### Data Source 

The data for this project has been provided by Cyclistic, and made available by Motivate International Inc. which operates the City of Chicago's Divvy bicycle-sharing service. To see the data source, please click [link](https://divvy-tripdata.s3.amazonaws.com/index.html). The Motivate Internations Inc. license can be viewed at [link](https://www.divvybikes.com/data-license-agreement).


### Preparation, processing, and analysis of the data

Downloaded and renamed the provided 12 zip files. Decided on the best analysis tools to use for this job. The combined datasets were too large to use Excel or Google Sheets, so I choose to use RStudio to clean, analyze, and create clear visualizations. 

Installed necessary packages.
- *install.packages("tidyverse")*
- *install.packages("lubridate")*
- *install.packages("ggplot2")*

Next, I downloaded the necessary libraries.
- *library("tidyverse")*
- *library("lubridate")*
- *library("ggplot2")*

I utilized the read_csv() function to read the 12 csv files containing Cyclistic's ride data.

- *data0122 <-read_csv("202201-divvy-tripdata 2.csv")*
- *data1221 <-read_csv("202112-divvy-tripdata.csv")*
- *data1121 <-read_csv("202111-divvy-tripdata.csv")*
- *data1021 <-read_csv("202110-divvy-tripdata.csv")*
- *data0921 <-read_csv("202109-divvy-tripdata.csv")*
- *data0821 <-read_csv("202108-divvy-tripdata.csv")*
- *data0721 <-read_csv("202107-divvy-tripdata.csv")*
- *data0621 <-read_csv("202106-divvy-tripdata.csv")*
- *data0521 <-read_csv("202105-divvy-tripdata.csv")*
- *data0421 <-read_csv("202104-divvy-tripdata.csv")*
- *data0321 <-read_csv("202103-divvy-tripdata.csv")*
- *data0221 <-read_csv("202102-divvy-tripdata.csv")*

Inspected data in various ways, using the following functions. 

- *View(cycle_trips)*
- *str(cycle_trips)*
- *head(cycle_trips)*
- *summary(cycle_trips)*
- *colnames(cycle_trips)*

I confirmed that all datasets have the same column names and proceeded to combine the 12 csv files into one dataset to make the data easier to work with. This was completed using the bind() function. 

- *cycle_trips<-bind_rows(data0122,data1221,data1121,data1021,data0921,data0821,data0721,data0621,data0521,data0421,data0321,data0221)*

Proceeded with renaming various columns, reformatted the dates column, and creating a "ride_length" column to the dataset. 

- *rename(cycle_trips
       ,start_time=started_at
       ,end_time=ended_at)*
- *cycle_trips $ date <- as.Date(cycle_trips $ started_at)*
- *cycle_trips $ month <- format(as.Date(cycle_trips $ date),'%m')*
- *cycle_trips $ day <- format(as.Date(cycle_trips $ date),'%d')*
- *cycle_trips $ year <- format(as.Date(cycle_trips $ date),'%Y')*
- *cycle_trips $ day_of_week <- format(as.Date(cycle_trips $ date),'%A')*
- *cycle_trips $ ride_length<-difftime(cycle_trips $ ended_at,cycle_trips$started_at)*       
- *cycle_trips $ ride_length<-as.numeric(as.character(cycle_trips $ ride_length))*

I reviewed the data again, using the summary function, to ensure all the changes the were made are working correctly.

- *summary(cycle_trips)*


Compared various pieces of data, including an analysis on ride length between casual riders and annual members to provide insights of differences between the two. Compared data on members verses casual users using the aggregate function. 

- *mean(cycle_trips$ride_length) #straight average (total ride length / rides)*
- *median(cycle_trips$ride_length) #midpoint number in the ascending array of ride lengths*
- *max(cycle_trips$ride_length) #longest ride*
- *min(cycle_trips$ride_length) #shortest ride*
- *aggregate(cycle_trips $ ride_length ~ cycle_trips $ member_casual, FUN = mean)*
- *aggregate(cycle_trips $ ride_length ~ cycle_trips $ member_casual, FUN = median)*
- *aggregate(cycle_trips $ ride_length ~ cycle_trips $ member_casual, FUN = max)*
- *aggregate(cycle_trips $ ride_length ~ cycle_trips $ member_casual, FUN = min)*
- *aggregate(cycle_trips $ ride_length ~ cycle_trips $ member_casual+
            cycle_trips$day_of_week, FUN = mean)*
            

In the results, I did notice that the days of the week were not in order so I used the following formula to reorder.

- *cycle_trips $ day_of_week<-ordered(cycle_trips$day_of_week,levels=c("Sunday","Monday","Tuesday","Wednesday","Thursday","Friday","Saturday"))*


### Findings with data visualizations

I used the following formula to produce a data visualization for the average ride duration for each day of the week.

- *cycle_trips%>% 
  mutate(weekday = wday(started_at, label = TRUE)) %>% 
  group_by(member_casual, weekday) %>% 
  summarise(number_of_rides = n()
            ,average_duration = mean(ride_length)) %>% 
  arrange(member_casual, weekday)  %>% 
  ggplot(aes(x = weekday, y = average_duration, fill = member_casual)) +
  geom_col(position = "dodge")+
  labs(title="Average ride duration", subtitle="Members vs. casual users")*
  

![Cyclistic_visual2](https://user-images.githubusercontent.com/102244119/161450022-a61be78b-a446-440a-85b6-19411d752b68.png)


I used the following formula to produce a data visualization for the number of rides for each day of the week.

- *cycle_trips%>% 
  mutate(weekday = wday(started_at, label = TRUE)) %>% 
  group_by(member_casual, weekday) %>% 
  summarise(number_of_rides = n()
            ,average_duration = mean(ride_length)) %>% 
  arrange(member_casual, weekday)  %>% 
  ggplot(aes(x = weekday, y = number_of_rides, fill = member_casual)) +
  geom_col(position = "dodge")+
  labs(title="Number of rides", subtitle="Members vs. casual users")*

![Cyclistic_Visual1](https://user-images.githubusercontent.com/102244119/161450036-43c4a180-268e-4a8e-bb17-aa52cc697c05.png)


I used the following formula to produce a data visualization for number of rides per month for each month.

- *cycle_trips%>% 
  group_by(member_casual, month) %>%  
  summarise(total_rides = n(),`average_duration_(mins)` = mean(ride_length)) %>% 
  arrange(member_casual) %>%
  ggplot(aes(x = month, y = total_rides, fill = member_casual)) +
  geom_col(position = "dodge") +
  labs(title= "Ride per month", subtitle="Members vs. casual users")*
  
![Cyclistic_Visual1](https://user-images.githubusercontent.com/102244119/161450052-7e67ff89-94b1-43ae-802e-c3feed44fcb7.png)


### Takeaways

- Members take a consistent number of rides throughout the week, pointing toward commuter use.
- Casual riders take a higher number of rides on Saturdays and Sundays, pointing toward recreational use. 
- Casual riders take longer rides than members, with more than twice the average duration of ride time.
- The peak months for causal riders are May through September, with members taking a higher number of rides from June through October.

### Recommendations

- Decrease the cost of single-ride and full-day passes for first-time and casual riders on weekdays. This will help encourage commuter use in new customers and casual riders, increasing overall memberships.
- Run promotions in marketing campaigns centered around winter riding to increase off-season use and increase memberships.
- Market the cost savings of an annual membership to casual riders, demonstrating the value of membership versus the cost of repeated single-ride or full-day passes.
