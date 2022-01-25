# Google-Capstone-Case-Studty-Bellabeat

--Checking for number of Rows on dailyActivities

Select Count(*)
From [dbo].[dailyActivity_merged$]
--940

--Checking for Duplicates in dailactivity set

Select Id, ActivityDate, TotalSteps, Count(*)
From [dbo].[dailyActivity_merged$]
group by id, ActivityDate, TotalSteps
Having Count(*) > 1
---None

Select *
From Google_Project..dailyActivity_merged$

Select Count(*)
From dailySteps_merged$
--940

Select Count(*)
From [dbo].[sleepDay_merged$]
--413

--Add sleep data columns into DailyActivities table

Alter Table [dbo].[dailyActivity_merged$]
Add total_mins_sleep int, total_minutes_bed int

--Add sleep records into dailyActivity table

UPDATE [dbo].[dailyActivity_merged$]
Set total_mins_sleep = t2.[TotalMinutesAsleep],
total_minutes_bed = t2.[TotalTimeInBed]
From [dbo].[dailyActivity_merged$] as t1
Full Outer Join sleepDay_merged$ as t2
on t1.id = t2.id and t1.ActivityDate = t2.SleepDay

Select * 
From [dbo].[dailyActivity_merged$]


--Sum of active minutes by day. Which day are people most active

Select WeekDays, SUM([VeryActiveMinutes]) as VActive
From [dbo].[dailyActivity_merged$]
Group by WeekDays

--Time Expenditure per day
---Pie Chart

Select Distinct WeekDays, SUM([SedentaryMinutes]) as sedentary_active_mins,
SUM([LightlyActiveMinutes]) as lightly_active_mins,
SUM([FairlyActiveMinutes]) as fairly_active_mins,
SUM([VeryActiveMinutes]) as very_active_mins
From [dbo].[dailyActivity_merged$]
Group by WeekDays

---Distances per Minutes given kind of Activity
--Scattorplot?

----Sed minute Average per user
Select Id, AVG([SedentaryMinutes]) as Sedentary_min
From [dbo].[dailyActivity_merged$]
Group by Id


Select Distinct Id, Weekdays, SUM([total_mins_sleep]) as Minutes_Asleep, SUM([total_minutes_bed]) as Minutes_In_Bed
From [dbo].[dailyActivity_merged$]
Where [total_minutes_bed] is not null
Group by Id, WeekDays

Select WeekDays, AVG([Calories]) as Avg_Cal
From [dbo].[dailyActivity_merged$]
Group by WeekDays
Order by WeekDays ASC;

Select WeekDays, AVG([TotalSteps]) as Avg_Steps
From [dbo].[dailyActivity_merged$]
Group by WeekDays
Order by WeekDays ASC;

Select WeekDays, AVG([TotalSteps]) as Avg_Steps
From [dbo].[dailyActivity_merged$]
Group by WeekDays
Order by WeekDays ASC;

Select Id, AVG([Calories]) as User_Cal
From [dbo].[dailyActivity_merged$]
Group by Id

-----Exploring total number of distinct users

Select COUNT(DISTINCT Id) as Distinct_Users 
From [dbo].[heartrate_seconds_merged$]
--7 distinct users

Select COUNT(DISTINCT Id) as Distinct_Users 
From [dbo].[hourlySteps_merged$]
--33 distinct users

Select COUNT(DISTINCT Id) as Distinct_Users 
From [dbo].[sleepDay_merged$]
--24 distinct users

Select COUNT(DISTINCT Id) as Distinct_Users 
From [dbo].[weightLogInfo_merged$]
--8 distinct users

--Creating table to calculate usage rate
--Usage rate = Active users/total days
Select [ActivityDate],
SUM(CASE When TotalSteps > 0 THEN 1 END) as Active_User,
SUM(CASE When TotalSteps >= 0 THEN 1 END) as Total
From [dbo].[dailyActivity_merged$]
Group by [ActivityDate]

Select Id,
SUM(CASE When TotalSteps > 0 THEN 1 END) as Active_User,
SUM(CASE When TotalSteps >= 0 THEN 1 END) as Total
From [dbo].[dailyActivity_merged$]
Group by Id

--Calculating Average of Daily Activity Minutes Per User
Select [Id],
AVG([VeryActiveMinutes]) as VActive_Minutes,
AVG([FairlyActiveMinutes]) as FActive_Minutes,
AVG(LightlyActiveMinutes) as LActive_Minutes,
AVG([SedentaryMinutes]) as Sed_Minutes
From [dbo].[dailyActivity_merged$]
Group by [Id]
