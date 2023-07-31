# CYCLISTIC BIKE SHARE CASE STUDY WITH PYTHON 
![Star Badge](https://img.shields.io/static/v1?label=%F0%9F%8C%9F&message=If%20Useful&style=style=flat&color=BC4E99)
[![View Main Folder](https://img.shields.io/badge/View-Main_Folder-971901?)](https://github.com/Ahmad-Zahoor/Cyclistic-Bike-Project)
[![View Repositories](https://img.shields.io/badge/View-My_Repositories-blue?logo=GitHub)](https://github.com/Ahmad-Zahoor?tab=repositories)
[![View My Profile](https://img.shields.io/badge/View-My_Profile-green?logo=GitHub)](https://github.com/Ahmad-Zahoor)

## 📕 Table Of Contents
* [SCENARIO](#scenario)
* [ASK](#ask)
* [PREPARE](#prepare)
* [PROCESS](#process)
* [ANALYZE](#analyze)
* [SHARE](#share)
* [ACT](#act)



## SCENARIO

> You are a junior data analyst working in the marketing analyst team at Cyclistic, a bike-share company in Chicago. The director of marketing believes the company's future success depends on maximizing the number of annual memberships. Therefore, your team wants to understand how casual riders and annual members use Cyclistic bikes differently. From these insights, your team will design a new marketing strategy to convert casual riders into annual members. But first, Cyclistic executives must approve your recommendations, so they must be backed up with compelling data insights and professional data visualizations.

     
## ASK
> In this phase, I will identify the main problem that facing by Cyclistic Bike company. I will also understand the needs of Stakeholders.

#### Guiding Questions

##### What is the Problem you are trying to solve?

##### Problem:
>Determine a way to build a profile for annual members and the best marketing strategies to turn casual bike riders into annual members.

##### How can your insights drive business decisions?
> The insights will help the marketing team to increase annual members.

#### Key tasks:

1. Identify the business task

2. Consider Key Stakeholders

#### Business tasks:

#### These are the questions/business task that would guide the future of the marketing program:

1.	To understand how annual members and casual riders use our Cyclistic bikes differently
2.	Why would casual members upgrade to annual memberships
3.	How can Cyclistic use digital media to influence casual riders to become members?

#### Stakeholders:

1: Lily Moreno, the director of marketing and my manager.
2: Cyclistic executive team.


## PREPARE

>In this phase, I will collect the Bike data from the below link:</br>
We will be using Cyclistic’s historical trip data [here](https://divvy-tripdata.s3.amazonaws.com/index.html) from 2021 January till 2021 December (202101-divvy-tripdata.zip -> 202112-divvy-tripdata.zip). We will be extracting all of our files into a folder “Divvy_Monthlytripdata” to organize and provide context.

>We will also be renaming the files to represent the data more clearly, it would also help with readability for other team members. Below is how I would do so:
(202101-divvy-tripdata.csv) -> (2021_01.csv)
(202102-divvy_tripdata.csv) -> (2021_02.csv)
And so on.

#### Code for Renaming all data files into given names:



``` r
import os

file_path = r'past your data file path here'
list_of_files = os.listdir(file_path)
```

```r
for file in list_of_files:
    
    base_name, extension = os.path.splitext(file)

    new_name = base_name[:4] + '_' + base_name[4:6] + '.csv'
    
    old_file = os.path.join(file_path, file)
    new_file = os.path.join(file_path, new_name)

    os.rename(old_file, new_file)
```

#### Guiding Questions:

**Where is your data located?**

>The data is located on this website: [Divvy-tripdata](https://divvy-tripdata.s3.amazonaws.com/index.html).

**How is the data organized?**

>The data is separated by month, each on its own csv.

**Are there issues with bias or credibility in this data? Does your data ROCCC?**

>Bias isn't a problem, the population of the dataset is its own clients as bike riders. And have full credibility for the same reason. And finally, it's ROCCC because it's reliable, original, comprehensive, current, and cited.

**How are you addressing licensing, privacy, security, and accessibility?**

>The company has its own license over the dataset. Besides that, the dataset doesn't have any personal information about the riders.

**How does it help you answer your question?**
>It may have some key insights about the riders and their riding style

**Are there any problems with the data?**
>It would be good to have some updated information about the bike stations. Also, more information about the riders could be useful.

#### Key tasks:

1. Download data and store it appropriately.
2. Identify how it’s organized.
3. Sort and filter the data.
4. Determine the credibility of the data.

#### Deliverable

- A description of all data sources used

>The main data source is 12 months (from January 2021 to December 2021) of riding data provided by the Cyclistic Company.

## PROCESS

>In this phase, I will clean the data by removing duplicates, handling missing values, identifying outliers, and making the data accurate for analysis.

#### Import libraries

```r
import numpy as np
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt
import plotly.express as px
from plotly.offline import download_plotlyjs, init_notebook_mode, plot, iplot
init_notebook_mode(connected=True)
import glob
```

#### Load Data

```r
all_csv_files = [i for i in glob.glob('./Divvy_Monthlytripdata/*.{}'.format('csv'))]
```

#### Reading all CSV Files

```r
all_dataframes = []

for file in all_csv_files:
    
    df = pd.read_csv(file)

    all_dataframes.append(df)
```

#### Fixing data types

```r
# Checking data types of all columns using info method
all_dataframes[0].info()
```

```
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 49622 entries, 0 to 49621
Data columns (total 13 columns):
 #   Column              Non-Null Count  Dtype  
---  ------              --------------  -----  
 0   ride_id             49622 non-null  object 
 1   rideable_type       49622 non-null  object 
 2   started_at          49622 non-null  object 
 3   ended_at            49622 non-null  object 
 4   start_station_name  45576 non-null  object 
 5   start_station_id    45576 non-null  object 
 6   end_station_name    44264 non-null  object 
 7   end_station_id      44264 non-null  object 
 8   start_lat           49622 non-null  float64
 9   start_lng           49622 non-null  float64
 10  end_lat             49408 non-null  float64
 11  end_lng             49408 non-null  float64
 12  member_casual       49622 non-null  object 
dtypes: float64(4), object(9)
memory usage: 4.9+ MB
```

>First part of the data cleaning process is to fix the data types of all the columns in order to make them easier to manipulate and more manageable. It should be noted that for several columns the data type was changed to strings, when the data types are displayed, they show up as objects as strings are a type of object in pandas.

```r
# Parse started_at, ended_at columns to DateTime
for df in all_dataframes:
    
    # started_at
    df['started_at'] = pd.to_datetime(df['started_at'], format="%Y-%m-%d %H:%M:%S")

    # ended_at
    df['ended_at'] = pd.to_datetime(df['ended_at'], format="%Y-%m-%d %H:%M:%S")

```

```r
# Now check the data types where we converted the started_at and ended_at columns into datetime type
all_dataframes[0].info()
```

```
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 49622 entries, 0 to 49621
Data columns (total 13 columns):
 #   Column              Non-Null Count  Dtype         
---  ------              --------------  -----         
 0   ride_id             49622 non-null  object        
 1   rideable_type       49622 non-null  object        
 2   started_at          49622 non-null  datetime64[ns]
 3   ended_at            49622 non-null  datetime64[ns]
 4   start_station_name  45576 non-null  object        
 5   start_station_id    45576 non-null  object        
 6   end_station_name    44264 non-null  object        
 7   end_station_id      44264 non-null  object        
 8   start_lat           49622 non-null  float64       
 9   start_lng           49622 non-null  float64       
 10  end_lat             49408 non-null  float64       
 11  end_lng             49408 non-null  float64       
 12  member_casual       49622 non-null  object        
dtypes: datetime64[ns](2), float64(4), object(7)
memory usage: 4.9+ MB
```

#### Combining Every Dataframe Into One Huge Dataframe

>We will combine all the data sets into one massive data set, so all necessary operations can be performed on this dataset.

```r
combined_df = pd.concat(all_dataframes)
```

```r
# Now see the first 5 records
combined_df.head()
```

```r
# Now check the shape of the combined dataset
combined_df.shape
```
#### Feature Creation

>After going through our business task, we need to add more columns for detail analysis:

**Day of the week**
```r
combined_df['day_of_week'] = combined_df['started_at'].dt.strftime('%a')
# Day order
day_order = ["Mon", "Tue", "Wed", "Thu", "Fri", "Sat", "Sun"]
# Convert 'day_of_week' column to categorical with a custom order
cat_dtype = pd.CategoricalDtype(categories=day_order, ordered=True)
combined_df['day_of_week'] = combined_df['day_of_week'].astype(cat_dtype)
```

**Start hour**

```r
combined_df['starting_hour'] = combined_df['started_at'].dt.strftime('%H')
```

**Month**
```r
combined_df['month'] = combined_df['started_at'].dt.strftime('%m')
```

**Trip Duration**
```r
combined_df['trip_duration'] = (combined_df['ended_at'] - combined_df['started_at'])/pd.Timedelta(minutes=1)
```

#### Remove those records whose trip_duration less than or equal to 0

```r
negative_record = combined_df[combined_df['trip_duration']<=1]

#check the shape of negative records
negative_record.shape

# now make new df without negative records
new_df = combined_df[combined_df['trip_duration']>1].reset_index().drop(columns='index')

# Again check the new df shape
new_df.shape
```

#### Identifying missing values

```r
round((new_df.isnull().sum()/len(new_df))*100,2)
```

```
ride_id                0.00
rideable_type          0.00
started_at             0.00
ended_at               0.00
start_station_name    12.17
start_station_id      12.17
end_station_name      12.97
end_station_id        12.97
start_lat              0.00
start_lng              0.00
end_lat                0.09
end_lng                0.09
member_casual          0.00
day_of_week            0.00
starting_hour          0.00
month                  0.00
trip_duration          0.00
dtype: float64

```

```r
# Now Remove null values
df_without_nulls = new_df.dropna()

# check the shape
df_without_nulls.shape
```

#### Handling duplicates

```r
duplicate_records = df_without_nulls[df_without_nulls.duplicated('ride_id')]

# display duplicate records
duplicate_records.head()
```
>There were no duplicate records in the dataset.

#### Handling outliers

```r
# from this code the statistics summary will not consist of any exponential value like 2e342.424
pd.set_option('display.float_format', lambda x: '%.2f' % x)

df_without_nulls.describe()
```
>Here you will see the trip_duration column which has a maximum 55944 (minutes) value which is an extreme value and it will affect our analysis.

```r
# Now convert the trip_duration data into percentile to check the data range in each percentile
import numpy as np

trip_duration = df_without_nulls['trip_duration']

ventiles = np.percentile(trip_duration, np.arange(0, 101, 5))
ventiles = np.around(ventiles, decimals=3)

output = ""

for i, ventile in enumerate(ventiles):
    output += f"{i * 5}%: {ventile} "

print(output)
```
>The above code will print the percentile range so the difference between 1% and 95% is 57.3 minutes. Because of that, in the analysis of this variable, we are going to use a subset of the dataset without outliers. The subset will contain 95% of the dataset.

```r
# Now keep 95% data that will clear of outliers
lower_percentile = np.percentile(df_without_nulls['trip_duration'], 1)
upper_percentile = np.percentile(df_without_nulls['trip_duration'], 95)

df_without_outlier = df_without_nulls[
    (df_without_nulls['trip_duration']>lower_percentile)&
    (df_without_nulls['trip_duration']<upper_percentile)]

```

```r
pd.set_option('display.float_format', lambda x: '%.2f' % x)
df_without_outlier.describe()

print("Removed", len(df_without_nulls) - len(df_without_outlier), "rows as outliers")

# check the shape
df_without_outlier.shape
```
#### Save the Clean data file
```r
df_without_outlier.to_csv('clean_data.csv', index=False)
```

#### Load the Clean data file
```r
new_df = pd.read_csv('clean_data.csv')
```

#### Guiding questions

**What tools are you choosing and why?**
>I'm using Python for this project, for two main reasons: Because of the large dataset and to gather experience with the language.

**Have you ensured your data’s integrity?**
>Yes, the data is consistent throughout the columns.

**What steps have you taken to ensure that your data is clean?**
>First the null values where removed, then the check the duplicate values but not found any duplicate values then remove outlier values.

**Have you documented your cleaning process so you can review and share those results?**
>Yes, it's all documented in this Python notebook.

#### Key tasks
1. Check the data for errors.
2. Choose your tools.
3. Transform the data so you can work with it efectively
4. Document the cleaning process

#### Deliverable
 - Documentation of any cleaning or manipulation of data


## ANALYZE

>Now it’s time to analyze the data and look for key information that we can perform analysis on.
>As mentioned just a moment ago, it is imperative to always remind yourself of the business task at hand during this stage. In order to answer our first business question, it would be beneficial to plot a few of our observations revolving around:

1. How much of the data is about members and how much is about casuals?
2. How do casual and members use their bikes differently throughout the week
3. Peak hours of bike usage between casual and annual members
4. Bike usage by each month
5. The average trip duration between casual and annual members
6. Rideable Type usage by Member Type

**1: How much of the data is about members and how much is about casuals?**

```r
pivot_table = pd.pivot_table(new_df,
                             index='member_casual',
                             values='ride_id',
                             aggfunc=[len, lambda x: (len(x) / len(new_df)) * 100],
                             margins=True,
                             margins_name='Total Count')

pivot_table.columns = ['Count', 'Percentage']

print(pivot_table)
```

```
                 Count  Percentage
member_casual                     
casual         1806265       42.44
member         2449406       57.56
Total Count    4255671      100.00
```
```r
# Group the data by member_casual
casual_members_data = new_df.groupby(['member_casual'])['ride_id'].count().reset_index()

```
```r
# Create a bar chart for member_casual to show its participant in data
px.bar(casual_members_data, y='member_casual', x='ride_id',
       title='Number of Rides by Member Type',
        color = 'member_casual', 
        height = 300,
        text = 'ride_id', 
        labels = {'ride_id': 'No. of Rides', 'member_casual': 'Member/Casual'},
        hover_name = 'member_casual', hover_data = {'member_casual': False, 'ride_id': True}, 
        color_discrete_map = {'casual': '#FF934F', 'member': '#058ED9'})

```
<p align="center">
     
![Alt text](Images/member_casual_bar_chart.png?raw=true "Bar chart")
<!--   <img src="/images/member_casual_bar_chart.png"> -->

>Observation:

>As we can see on the member x casual table, members have a bigger proportion of the dataset, composing ~57%, ~12% bigger than the count of casual riders.

**2: How do casual and members use their bikes differently throughout the week**

```r
# Day order
day_order = ["Mon", "Tue", "Wed", "Thu", "Fri", "Sat", "Sun"]
# Convert the 'day_of_week' column to categorical with a custom order
cat_dtype = pd.CategoricalDtype(categories=day_order, ordered=True)
new_df['day_of_week'] = new_df['day_of_week'].astype(cat_dtype)
```

```r
# Group the data by day_of_week and member_casual
day_of_week = new_df.groupby(['day_of_week','member_casual'])['ride_id'].count().reset_index()
```
```r
px.line(day_of_week, x='day_of_week', y='ride_id',
        title='Riders usage by Member type in Whole Week',
        color='member_casual',
        line_shape='spline',
        markers=True,
        labels={'ride_id': 'No. of Rides', 'day_of_week': 'Week Days', 'member_casual': 'Member/Casual'},
        hover_name='member_casual', hover_data={'member_casual': False,'day_of_week':True, 'ride_id': True},
        color_discrete_map={'casual': '#FF934F', 'member': '#058ED9'},
        category_orders={'day_of_week': day_order}
       )
```

<p align="center">
     <img src="/Images/whole_weekdays.png">
<!-- ![Alt text](Images/whole_weekdays.png?raw=true "Bar chart") -->

>Observation:

>Here, we can find that overall ridership for annual members is fairly stable across the week. Such would indicate that there is a possibility that annual members are using bikes as their main mode of transportation.

>On the other hand, ridership for casual members is fairly low on the weekdays but starts to ramp up on Fridays and eventually peaks on Saturdays.

**3: Peak hours of bike usage between casual and annual members**

>We first see the Peak hours of bike usage between casual and annual members on the weekdays.

```r
weekday_names = ["Mon", "Tue", "Wed", "Thu", "Fri"]  # List of weekday names

# Filter the data frame to keep only the weekday records
weekday_df = new_df[new_df['day_of_week'].isin(weekday_names)]
```

```r
# Group data by starting_hour and member_casual
peak_hours_weekdays = weekday_df.groupby(['starting_hour', 'member_casual'], as_index=False).count()
```

```r
peak_hour_fig = px.line(peak_hours_weekdays, x='starting_hour', y='ride_id',
                 title='Hourly usage of bikes on the weekdays',
                 color='member_casual',
                 line_shape='spline',
                 markers=True,
                 labels={'ride_id':'No. of Rides', 'starting_hour':'24 Hours', 'member_casual':'Member/Casual'},
                 hover_name='member_casual', hover_data={'member_casual':False, 'month':False, 'ride_id': True},
                 color_discrete_map={'casual':'#FF934F', 'member':'#058ED9'},
                #  category_orders={'starting_hour': hour_order
                #                   }
                )

peak_hour_fig.update_xaxes(range=[0, 23], dtick=1)
peak_hour_fig.show()
```

<p align='center'>
![Alt Text](Images/hourly_by_weekdays.png?raw=true "Line chart")
<!-- <img src="/images/hourly_by_weekdays.png"> -->

>Observation:

>Based on the weekday graph, it would further reinforce my previous hypothesis whereby annual members are working adults, as we can see from:

- 7 am-8 am: A rally in usage, which could indicate when they’ve begun commuting to work
- 12 pm: An increase in usage, which would indicate lunch hour
- 5 pm: A peak in usage, which again falls in line with the office off-hours


>Now see the Peak hours of bike usage between casual and annual members on the weekends.

```r
weekendays = ["Sat", "Sun"]

weekend_df = new_df[new_df['day_of_week'].isin(weekendays)]
```

```r
# Group data by starting_hour and member_casual
group_weekendays = weekend_df.groupby(['starting_hour', 'member_casual'], as_index=False).count()
```

```r
weenkend_fig = px.line(group_weekendays, x='starting_hour', y='ride_id',
                       color='member_casual',
                       title='Hourly usage of bikes on the weekends',
                       line_shape='spline',
                       markers=True,
                       labels={'ride_id':'No. of Riders', 'member_casual': 'Member/Casual', 'starting_hour':'24 Hours'},
                       hover_name='member_casual', hover_data={'member_casual':False, 'ride_id':True},
                       color_discrete_map={'casual':'#FF934F', 'member':'#058ED9'})

weenkend_fig.update_xaxes(range=(0,23), dtick=1)
weenkend_fig.show()
```

<p align='center'>
![Alt Text](Images/hourly_by_weekendays.png?raw=true "Line chart")
<!-- <img src="/images/hourly_by_weekendays.png"> -->

>Observation:

>The weekends, on the other hand, see a dramatic increase in ridership for the casual members starting from 11 am and peaks in the afternoon.


**4: Bike usage by each month**

```r
# Group data by month and member_casual
month_group = new_df.groupby(['month', 'member_casual'], as_index=False).count()
```
```r
month_fig = px.line(month_group, x='month', y='ride_id',
                    color='member_casual',
                    title='Bike usage by Member Type in each Month',
                    line_shape='spline',
                    markers=True,
                    labels={'ride_id':'No. of Riders', 'member_casual':'Member/Casual', 'month': 'Months (Jan 2021 - Dec 2021)'},
                    hover_name='member_casual', hover_data={'member_casual':False, 'ride_id':True, 'month':True},
                    color_discrete_map={'casual':'#FF934F', 'member':'#058ED9'})

month_fig.update_xaxes(range=[1,12], dtick=1)
month_fig.show()
```

<p align='center'>
![Alt Text](Images/member_type_month.png?raw=true "Line chart")
<!-- <img src='images/member_type_month.png'> -->

>Observation:

>As we can see, ridership starts to freefall during the later months of the year. This is possible due to the change of seasons as usually in the months of October, the temperature starts to drop and the possibility of snow entails after.

>Interestingly, casual member ridership peaks in the month of July. Knowing that schools in Chicago end around the middle of July, and resume at the end of August, we could hypothesize that the majority of the said users are not high school students.

**5: The average trip duration between casual and annual members**

```r
average_group = new_df.groupby(['day_of_week', 'member_casual'], as_index=False)['trip_duration'].mean()
average_group['trip_duration'] = average_group['trip_duration'].round(2)
```

```r
average_fig = px.bar(average_group, x='day_of_week', y='trip_duration',
                     color='member_casual',
                     title='Average Trip duration usage by Member Type in a whole week',
                     labels={'trip_duration':'Average Trip Duration', 'member_casual':'Member/Casual', 'day_of_week':'Week Days'},
                     hover_name='member_casual', hover_data={'member_casual':False, 'day_of_week':True, 'trip_duration':True},
                     color_discrete_map={'casual':'#FF934F', 'member':'#058ED9'}
                     )

average_fig.show()

```

<p align='center'>
![Alt Text](Images/average_trip_duration.png?raw=true "Bar chart")
<!-- <img src='images/average_trip_duration.png'> -->

>Observation:

>Here, we see that casual members have significantly longer average trip durations than annual members, nearly double in fact. This could signify that casual members are mainly using the bikes for leisure and or possibly sports activities.

>Whereas annual members would very likely use the bikes to commute from their living quarters to their offices and vice versa.

**6: Rideable Type usage by Member Type**

```r
rideable_type_group = new_df.groupby(['rideable_type', 'member_casual']).agg(
    count=('ride_id', 'size'),
    percent = ('ride_id',lambda x: (len(x)/len(new_df))*100),
    ).reset_index()

rideable_type_group['percent'] = rideable_type_group['percent'].round(2).astype(str) + '%'
```

```r
rideable_type_fig = px.bar(rideable_type_group, y='rideable_type', x='count',
                           color='member_casual',
                           title='Rideable Type usage by Member Type',
                           labels={'count': 'Count', 'rideable_type': 'Rideable Type', 'member_casual':'Member/Casual'},
                           hover_name='member_casual', hover_data={'member_casual':False, 'percent':True, 'count':True},
                           color_discrete_map={'casual':'#FF934F', 'member':'#058ED9'}
                           )

rideable_type_fig.show()
```

<p align='center'>
![Alt Text](Images/rideable_type.png?raw=true "Bar chart")
<!-- <img src='images/rideable_type.png'> -->

>Observations:

- Members have a bigger preference for classic bikes and electric bikes which show ~44% for classic_bike and ~12% for electric_bike.
- Casuals have ~26% preference for classic bikes and 16% for electric bikes.
- Casual also use the docked_bike by ~5% but Member dont use the docked_bike.

#### Guiding questions

**How should you organize your data to perform analysis on it?**
The data has been organized into a single CSV concatenating all the files from the dataset.

**Has your data been properly formatted?**

>Yes, all the columns have their correct data type.

>One of the main surprises is how members differ from casuals when analyzed on weekdays. Also that members have less riding time than casual.

**What surprises did you discover in the data?**

>One of the main surprises is how members differ from casuals when analyzed on weekdays. Also that members have less riding time than casual.

**What trends or relationships did you find in the data?**

- There are more members than casuals in the dataset.
- There are more data points in July 2021.
- There is more of a difference between the flow of members/casual from midweek to weekends.
- Members use bikes on schedules that differ from casual.
- Members have less riding time.
- Members tend to prefer classic bikes.

**How will these insights help answer your business questions?**

> This insight helps to build a profile for members.

#### Key tasks

1. Aggregate your data so it’s useful and accessible.
2. Organize and format your data.
3. Perform calculations.
4. Identify trends and relationships.


#### Deliverable

>A summary of your analysis


### SHARE

>Let's go through the main finds and try to arrive at a conclusion.

>What we know about the dataset:

- Members have the biggest proportion of the dataset, ~12% bigger than casuals.
- The month with the biggest count of data points was July.
- In all months we have more members' rides than casual rides.
- The difference in the proportion of member x casual is smaller in the last semester of 2021.
- Temperature heavily influences the volume of rides in the month.
- The biggest volume of data is on the weekend.
- There's a bigger volume of bikers in the afternoon.

>It's possible to notice that the distribution of rides by month is cyclical through the years, it's influenced by the temperature. The remaining question is: Why are there more members than casual? One plausible answer is that members have a bigger need for bikes than casuals, as can be seen in how there are more members than casuals in cold months.

>Besides that, we have more bike rides on the weekends. Maybe because in those days bikes were utilized in more recreational ways. This is even more plausible when knowing that There's a bigger volume of bikers in the afternoon.

>Now for how members differ from casuals:

- Members may have the biggest volume of data, besides on Saturday. On this weekday, casuals take place as having the most data points.
- Weekends have the biggest volume of casuals, starting on Friday, a ~20% increase.
- We have more members during the morning, mainly between 5 am and 11 am.
- There's a big increase of data points in the midweek between 6 am to 8 am for members. Then it fell a bit. Another big increase is from 4 pm to 5 pm.
- During the weekend we have a bigger flow of casuals between 11 am to 4 pm.
- Members have a bigger preference for classic bikes, 44% more.
- Casuals have more riding time than members.
- Riding time for members keeps unchanged during the midweek, increasing during weekends.
- Casuals follow a more curve distribution, peaking on Sundays and volleying on Wednesdays/Thursdays.

>What we can take from this information is that members have a more fixed use for bikes besides casuals. Their uses are for more routine activities, like:

- Go to work.
- Use it as an exercise

>This can be proven we state that we have more members between 6 am to 8 am and from 5 pm to 6 pm. Also, members may have set routes when using the bikes, as proven by riding time for members keeps unchanged during the midweek, increasing during weekends. The bikes are also heavily used for recreation on the weekends when riding time increases and casuals take place.

>Members also have a bigger preference for classic bikes, so they can exercise when going to work.

>Concluding:

- Members use the bikes for fixed activities, one of those is going to work.
- Bikes are used for recreation on the weekends.
- Rides are influenced by temperature.

#### Guiding questions

**Were you able to answer the question of how annual members and casual riders use Cyclistic bikes differently?**

>Yes. The data points to several differences between casuals and members.

**What story does your data tell?**

>The main story the data tells is that members have set schedules, as seen in timestamps on the weekday chart. Those timestamps point out that members use the bikes for routine activities, like going to work. The average time Chart also point out that they have less riding time because they have a set route to take.

**How do your findings relate to your original question?**
>The findings build a profile for members, relating to "Find the key differences between casuals and annual riders", also knowing why they use the bikes helps to find "How digital media could influence them".

**Who is your stakeholders? What is the best way to communicate with them?**

>The main target stakeholders are my cyclistic marketing analytics team and Lily Moreno. The best way to communicate is through a slide presentation of the findings.

**Can data visualization help you share your findings?**

>Yes, the main core of the finds is through data visualization.

**Is your presentation accessible to your stakeholders?**

>Yes, the plots were made using vibrant colors, and corresponding labels.

#### Key tasks

1. Determine the best way to share your findings.
2. Create effective data visualizations.
3. Present your findings.
4. Ensure your work is accessible.

#### Deliverable

- Supporting visualizations and key findings


## ACT
>The act phase would be done by the marketing team of the company. The main takeaway will be the top three recommendations for marketing.

#### Guiding questions

**What is your final conclusion based on your analysis?**

>Members and casual have different habits when using the bikes. The conclusion is further stated in the share phase.

**How could your team and business apply your insights?**

>The insights could be implemented when preparing a marketing campaign for turning casual into members. The marketing can focus on workers as a green way to get to work.

**What next steps would you or your stakeholders take based on your findings?**

>Further analysis could be done to improve the findings, besides that, the marketing team can take the main information to build a marketing campaign.

**Is there additional data you could use to expand on your findings?**

1. Mobility data.
2. Improved climate data.
3. More information members.

#### Deliverable

- Your top three recommendations based on your analysis

1. Build a marketing campaign focusing on showing how bikes help people to get to work while maintaining the planet green and avoiding traffic. The ads could be shown on professional social networks.
2. Increase benefits for riding during cold months. Coupons and discounts could be handed out.
3. As the bikes are also used for recreation on the weekends, ad campaigns could also be made showing people using the bikes for exercise during the week. The ads could focus on how practical and consistent the bikes can be.
