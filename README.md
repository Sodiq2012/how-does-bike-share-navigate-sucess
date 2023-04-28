# how-does-bike-share-navigate-sucess
This is a data analysis project for cyclistics, a bike-share company.
Ther are two customer type for this company; casual and member.
casual are customer type that use the bike for some hours in a day while member are customer type that puchase the annual membership ticket.
 KEY BUSINESS GOAL: Design a marketing strategy to convert casual customer to member type.
 
 The data was collected from kaggle and clean with python 
 
 
 import pandas as pd
import numpy as np
%matplotlib inline
import matplotlib.pyplot as plt
import seaborn as sns
df1 = pd.read_csv("202201-divvy-tripdata.csv")
df2 =  pd.read_csv("202202-divvy-tripdata.csv")
df3 =  pd.read_csv("202203-divvy-tripdata.csv")
df4 =  pd.read_csv("202204-divvy-tripdata.csv")
df5 =  pd.read_csv("202205-divvy-tripdata.csv")
df6 =  pd.read_csv("202206-divvy-tripdata.csv")
df7 =  pd.read_csv("202207-divvy-tripdata.csv")
df8 =  pd.read_csv("202208-divvy-tripdata.csv")
df9 =  pd.read_csv("202209-divvy-publictripdata.csv")
df10 =  pd.read_csv("202210-divvy-tripdata.csv")
df11 =  pd.read_csv("202211-divvy-tripdata.csv")
df12 =  pd.read_csv("202212-divvy-tripdata.csv")
df= pd.concat([df1,df2,df3,df4,df5,df6,df7,df8,df9,df10,df11,df12],ignore_index=True)# combine all the dfs

df = df.drop(['start_station_name', 'start_station_id', 
         'end_station_name','end_station_id',
         'start_lat', 'start_lng', 'end_lat', 'end_lng'], axis=1) #drop columns that are not usuful for the analysis

df[['started_at', 'ended_at']] = df[['started_at', 'ended_at']].apply(pd.to_datetime) #convert the dateframe object or 
#strings objects to datetime object
df['ride_length'] = (df.ended_at - df.started_at)/pd.Timedelta(seconds=1) #create ride_lenght column and calc. time diff.
# btw started_at and ended_at.
df['day_of_week'] = df['started_at'].dt.dayofweek #extract the day of the week from started_at(datetime object) column
# 0-Monday and 6-Sunday
df['weekday'] = df['started_at'].dt.day_name() #get the weekday as a string.
df['started_at_month'] = df['started_at'].dt.month
df['started_at_monthName'] = df['started_at'].dt.month_name()

df = df.sort_values(by=['member_casual', 'started_at_month','weekday'])

month_average_ride_length = df.groupby('started_at_monthName').mean().drop(['day_of_week','started_at_month'], axis=1)
month_average_ride_length = month_average_ride_length.sort_values(by='ride_length')
month_average_ride_length
# key insight : june has the maximum average ride length while december has the minimum average ride lenght
month_average_ride_length.plot(kind='bar', color="green")
plt.title("AVERAGE RIDE LENGTH BY MONTH")
plt.ylabel("Ride Length")
plt.xlabel("Month Name")
plt.show()
month_average_ride_length_by_user = df.groupby([ 'member_casual', 'started_at_monthName']).mean('ride_lenght').drop(['day_of_week','started_at_month'], axis=1)
month_average_ride_length_by_user  = month_average_ride_length_by_user .sort_values(by='ride_length')
month_average_ride_length_by_user
m_a_r_l_b_u = month_average_ride_length_by_user
m_a_r_l_b_u
# key insight : 
# 1. june has the maximum average ride length while december has the minimum average ride lenght for member.
# 2. March has the maximum average ride length while November has the minimum average ride lenght for casual
month_average_ride_length_by_user.plot(kind='bar', title="AVERAGE RIDE LENGTH BY CUSTOMER TYPE",stacked=True)
plt.ylabel("Month Name")
plt.xlabel("Customer Type")
plt.show()
Average_ride_lenght_by_rideable_type = df.groupby(['member_casual','weekday','rideable_type']).mean().drop(['day_of_week','started_at_month'], axis=1)
Average_ride_lenght_by_rideable_type
month_mode_day_of_week =  df.groupby(['started_at_monthName','member_casual']).max('ride_lenght').drop(['started_at_month'], axis=1)
month_mode_day_of_week = month_mode_day_of_week.sort_values(by='ride_length')
month_mode_day_of_week
# key insight:
# 1. the day of week with maximum ride lenght for both casual and member is Saturday for all month.
# 2. casual has the maximum ride lenght for all month.
# 3. almost the same maximum ride lenght for member for all month
# 4. october has the highest maximum ride lenght while February has the lowest maximum ride length
# key takehome: saturday is a good business day for both casual and member in all month.
df.describe().T
