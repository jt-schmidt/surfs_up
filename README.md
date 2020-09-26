# Surfs_Up
Module 9 -- SQLITE, SQL Alchemy, Flask project

## Overview
In this module's course & challenge, various exercises performed are:
- Query data from a SQLITE database file using SQL Alchemy in Python
- Build a dataframe using Pandas
- Plot data using MatPlotLib
- Generate local web page content from Python using Flask

For the challenge in particular, SQLITE database of 2 tables is provided:
- Measurement
  - Station ID where data 
  - Date of data collection
  - Precipitation data
  - Temperature data
- Station
  - Station ID
  - Station Name
  - Station geographical info (latitude, longitude, elevation)

Objective are to:
1. Query temperature data for all June months and place into a Pandas Data Frame to provide a statistical description.
2. Repeat for all December months.
3. Analysis comparing results from #1 and #2 including 2 additional queries for proposed analysis. 

## Results

Base statistical comparison between temperatures in June & December for all years 2010 ~ 2017.

![June_December_Summary](/June_December_Summary.png)

Key observations:
1.  There are 183 additional data points in June than December.  This is surprising considering a difference between months by only 1 day.  
    - It is recommended to perform deeper analysis to understand the data collection delta between the months.
2.  Max, Mean & Standard Deviation in temperatures are very stable despite summer & winter months with temperature swing of only 2F, 4F and 0.5F, respectively.
3.  Largest temperature difference is in the Minimum experienced with a low of 56F in December.  
4.  I want to live in Hawaii.

## Summary



```SQL
--Raw SQL for 1st Query
SELECT
	substr(m.date,1,4) as year,
	substr(m.date,6,2) as month,
	count(m.tobs) as count_temp,
	min(m.tobs) as min_temp,
	avg(m.tobs) as avg_temp,
	max(m.tobs) as max_temp
from measurement m
group by
	substr(m.date,1,4),
	substr(m.date,6,2)
```

```Python
#Using SQL Alchemy for 1st Query
first_results = session.query(
        func.substr(Measurement.date,1,4).label('year'),
        func.substr(Measurement.date,6,2).label('month'),
        func.count(Measurement.tobs).label('count_temp'),
        func.min(Measurement.tobs).label('min_temp'),
        func.avg(Measurement.tobs).label('avg_temp'),
        func.max(Measurement.tobs).label('max_temp')
).group_by(func.substr(Measurement.date,1,4),func.substr(Measurement.date,6,2)).all()
```

```SQL
--Raw SQL for 2nd Query
SELECT
	s.name,
	substr(m.date,6,2) as month,
	count(m.tobs) as count_temp,
	min(m.tobs) as min_temp,
	avg(m.tobs) as avg_temp,
	max(m.tobs) as max_temp
from measurement m
inner join station s on m.station = s.station
WHERE
	m.date like '%-06-%' or 
	m.date like '%-12-%'
group by
	s.name,
	substr(m.date,6,2)
```
```Python
#Using SQL Alchemy for 2nd Query
second_results = session.query(
        Station.name,
        func.substr(Measurement.date,6,2).label('month'),
        func.count(Measurement.tobs).label('count_temp'),
        func.min(Measurement.tobs).label('min_temp'),
        func.avg(Measurement.tobs).label('avg_temp'),
        func.max(Measurement.tobs).label('max_temp')
).join(Station, Measurement.station == Station.station
).filter(or_(Measurement.date.like('%-12-%'),Measurement.date.like('%-06-%'))
).group_by(Station.name,func.substr(Measurement.date,6,2)).all()
```
<!---
Deliverable 1
--A working query is written to retrieve the June temperatures from the date column of the Measurement table. (10 pt)
--The temperatures are added to a list. (10 pt)
--The list of temperatures is converted to a Pandas DataFrame. (10 pt)
--Summary statistics are generated for the DataFrame. (10 pt)
--->
<!---
Deliverable 2
--A working query is written to retrieve the December temperatures from the date column of the Measurement table (10 pt)
--The temperatures are added to a list. (10 pt)
--The list of temperatures is converted to a Pandas DataFrame. (10 pt)
--Summary statistics are generated for the DataFrame. (10 pt)
--->
<!---
Deliverable 3
--There is a title, and there are multiple sections. (2 pt)
--Each section has a heading and subheading. (2 pt)
--Links to images are working and displayed correctly. (2 pt)
--Overview:  The purpose of the analysis is well defined. (3 pt)
--Results:  There is a bulleted list that addresses the three key differences in weather between June and December. (6 pt)
--Summary:  There is a high-level summary of the results and there are two additional queries to perform to gather more weather data for June and December. (5 pt)
--->
