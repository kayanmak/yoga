Dataset: https://www.kaggle.com/datasets/thedevastator/how-does-daily-yoga-impact-screen-time-habits

### Step 1 - Ask 
1. Explore the relationship between day of the week and overall or category-specific screen time.
2. Track the impact of daily yoga on overall and category-specific screen time.  



### Step 2 - Prepare
```The 'index' and 'Date' columns are both unique keys to this table.```
SELECT 
COUNT(DISTINCT index),
COUNT(DISTINCT Date)
FROM optimal-spark-392913.yoga.screen_time;
```Both 'index' and 'Date' columns contain 28 distinct entries.``` 

```Check if there is duplicated data in the 'index' or 'Date' column to ensure data integrity.```
SELECT index, COUNT(*)
FROM optimal-spark-392913.yoga.screen_time
GROUP BY index
HAVING COUNT(*) > 1;

SELECT Date, COUNT(*)
FROM optimal-spark-392913.yoga.screen_time
GROUP BY Date
HAVING COUNT(*) > 1;
```There is no data to show for both columns, i.e. no duplicated data in both 'index' and 'Date' columns.```

### Step 3 - Process
```Check how many entries on each day of the week in this dataset.``` 
SELECT Week_Day, COUNT(*)
FROM optimal-spark-392913.yoga.screen_time
GROUP BY Week_Day
HAVING COUNT(*) > 1;
```Among the 28 distinct entries, there are four entries for each day of the week. This shows the data was collected everyday during the indicated period of the study.```


```Find out if there is prefered or unprefered weekday(s) for yoga during the week```
SELECT COUNT(Week_Day),
  CASE
    WHEN yoga = 1 THEN 'Yes'
    WHEN yoga = 0 THEN 'No'
    ELSE NULL  -- Handle any unexpected values
  END AS Daily_yoga -- give an output column name
FROM optimal-spark-392913.yoga.screen_time
GROUP BY yoga
```During 28 days of the data collection, the subject did yoga for 16 days whereas the other 12 days the subject did not do yoga.```

SELECT Week_Day,COUNT(Week_Day) AS frequency,
  CASE
    WHEN yoga = 1 THEN 'Yes'
    WHEN yoga = 0 THEN 'No'
    ELSE NULL  -- Handle any unexpected values
  END AS Daily_yoga -- give an output column name 
FROM optimal-spark-392913.yoga.screen_time
GROUP BY yoga, Week_Day
ORDER BY
  CASE
    WHEN Week_Day = 'Sunday' THEN 1
    WHEN Week_Day = 'Monday' THEN 2
    WHEN Week_Day = 'Tuesday' THEN 3
    WHEN Week_Day = 'Wednesday' THEN 4
    WHEN Week_Day = 'Thursday' THEN 5
    WHEN Week_Day = 'Friday' THEN 6
    WHEN Week_Day = 'Saturday' THEN 7
    ELSE 8 -- To handle any unexpected values
  END;
```No weekday had zero yoga activity, nor any weekday always with yoga, i.e. the subject did not show particular weekday preference for yoga.```

### Step 4 - Analyze 
```Investigate the relationship between day of the week and overall or category-specific screen time```
SELECT Week_Day,
  AVG(Total_Screen_Time_) AS average_total_screen_time, 
  AVG(Social_Networking) AS average_social_networking,
  AVG(Reading_and_Reference) AS average_read,
  AVG(Other) AS average_other,
  AVG(Productivity) AS average_productivity,
  AVG(Health_and_Fitness) AS average_health_fitness,
  AVG(Entertainment) AS average_entertainment,
  AVG(Creativity) AS average_creativity
FROM optimal-spark-392913.yoga.screen_time
GROUP BY
  Week_Day
ORDER BY
  CASE
    WHEN Week_Day = 'Sunday' THEN 1
    WHEN Week_Day = 'Monday' THEN 2
    WHEN Week_Day = 'Tuesday' THEN 3
    WHEN Week_Day = 'Wednesday' THEN 4
    WHEN Week_Day = 'Thursday' THEN 5
    WHEN Week_Day = 'Friday' THEN 6
    WHEN Week_Day = 'Saturday' THEN 7
    ELSE 8 -- To handle any unexpected values
  END;

```
Based on the average screen time (overall and category-specific), the overall screen time and screen time for social networking show the same pattern: the average time increases from Sunday to Monday, followed by a slight drop on Tuesday and then reaches the peak of the week on Wednesday. After that, the average fluctuates going down and up from Thursday to Saturday. 

The productivity and others categories also the maximum average screen time on Wednesday. 

In particular, productivity screen time shows a unique pattern that follows a normal distribution across the week from Sunday to Saturday. 

Reading and entertainment both have the highest average screen time on Friday, whereas health and fitness is on Monday. 

For creativity, the screen time shows the same level on Friday, Saturday and Sunday, whereas null value for the rest of the week. 

Of note, among all categories, the highest screen time everyday throughout the week is spent on social networking. 
```


``` Investigate the impact of daily yoga on overall and category-specific screen time.```
SELECT
  CASE
    WHEN yoga = 1 THEN 'Yes'
    WHEN yoga = 0 THEN 'No'
    ELSE NULL  -- Handle any unexpected values
  END AS Daily_yoga, -- give an output column name
  AVG(Total_Screen_Time_) AS average_total_screen_time,
  AVG(Social_Networking) AS average_social_networking,
  AVG(Reading_and_Reference) AS average_read,
  AVG(Other) AS average_other,
  AVG(Productivity) AS average_productivity,
  AVG(Health_and_Fitness) AS average_health_fitness,
  AVG(Entertainment) AS average_entertainment,
  AVG(Creativity) AS average_creativity
FROM
  optimal-spark-392913.yoga.screen_time
WHERE
  Yoga IN (0, 1)
GROUP BY
  Yoga;
```
With daily yoga, the average overall screen time decreases. 

In addition, the average screen time for categories social networking, reading, health and fitness, entertainment, and others decreases when there is daily yoga. 

The average screen time for productivity has a similar level with or without daily yoga. 

On the other hand, the average screen time for creativity increases by 3.8 fold when the user had daily yoga  compared to no yoga (0.31 vs 0.08 average time).
``` 


```Investigate the impact of daily yoga and overall or category-specific screen time on different days of the week```
SELECT 
  CASE
    WHEN yoga = 1 THEN 'Yes'
    WHEN yoga = 0 THEN 'No'
    ELSE NULL  -- Handle any unexpected values
  END AS Daily_yoga, -- give an output column nameWeek_Day,
  Week_Day,
  AVG(Total_Screen_Time_) AS average_total_screen_time, 
  AVG(Social_Networking) AS average_social_networking,
  AVG(Reading_and_Reference) AS average_read,
  AVG(Other) AS average_other,
  AVG(Productivity) AS average_productivity,
  AVG(Health_and_Fitness) AS average_health_fitness,
  AVG(Entertainment) AS average_entertainment,
  AVG(Creativity) AS average_creativity
FROM optimal-spark-392913.yoga.screen_time
WHERE 
Yoga IN (0, 1)
GROUP BY
  Yoga,
  Week_Day
ORDER BY
  CASE
    WHEN Week_Day = 'Sunday' THEN 1
    WHEN Week_Day = 'Monday' THEN 2
    WHEN Week_Day = 'Tuesday' THEN 3
    WHEN Week_Day = 'Wednesday' THEN 4
    WHEN Week_Day = 'Thursday' THEN 5
    WHEN Week_Day = 'Friday' THEN 6
    WHEN Week_Day = 'Saturday' THEN 7
    ELSE 8 -- To handle any unexpected values
  END;
```
With daily yoga, the average overall screen time decreases everyday except Sundays and Thursdays. 

In addition, the average screen time for categories social networking, reading, entertainment, and others decreases when there is daily yoga. However, similar to the overall screen time, exceptions on certain week days in the category-specific screen time can be observed despite the decreasing trend with daily yoga. 

However, despite the decreasing trend for health and fitness screen time with daily yoga, there is higher average with daily yoga on Mondays (4.0 to 5.0) and Fridays (0.0 vs 1.0) but the average reduces with daily yoga from 15 to null on Tuesdays. Otherwise, the average remains at null value the rest of the week regardless of daily yoga. 

The average screen time for productivity has a similar level with or without daily yoga, but a higher average can be observed on five days of a week except Wednesdays and Saturdays. 

Regarding the average creativity screen time on each day of the week, it can be observed that the average is higher with daily yoga on Fridays and Saturdays (0.0 vs 1.0). The average on Sundays remains the same regardless of daily yoga (0.5). Otherwise, the average remains at null value the rest of the week regardless of daily yoga. 
```
