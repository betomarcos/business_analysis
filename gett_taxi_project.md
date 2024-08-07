# Analysis of Taxi Driver App Cancellations
Project from: https://platform.stratascratch.com/data-projects/insights-failed-orders

_Gett, previously known as GetTaxi, is an Israeli-developed technology platform solely focused on corporate Ground Transportation Management (GTM). They have an application where clients can order taxis, and drivers can accept their rides (offers). At the moment, when the client clicks the Order button in the application, the matching system searches for the most relevant drivers and offers them the order. In this task, we would like to investigate some matching metrics for orders that did not completed successfully, i.e., the customer didn't end up getting a car._

Notes
* order = when a client requests a taxis
* offer = when a driver accepts a ride

## Data Description
We have two data sets: data_orders and data_offers, both being stored in a CSV format.  

**data_orders**  
<img width="999" alt="image" src="https://github.com/user-attachments/assets/5dc8d3ea-ac92-4a76-8e47-b9f3624a49c5">


**data_offers**  
<img width="213" alt="image" src="https://github.com/user-attachments/assets/db60e175-e2f1-4ecb-8d5d-2a8d449bf1e9">




----------------------------------------------------------------
## SQL Create statements

To start the project, the CSV data was exported and loaded to a Mysql database. Below the queries to create the 2 tables.
<br/>
``` sql
CREATE TABLE `gett_offers` (
  `order_gk` bigint DEFAULT NULL,
  `offer_id` bigint DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;
```

``` sql
CREATE TABLE `gett_orders` (
  `order_datetime` text,
  `origin_longitude` double DEFAULT NULL,
  `origin_latitude` double DEFAULT NULL,
  `m_order_eta` text,
  `order_gk` bigint DEFAULT NULL,
  `order_status_key` int DEFAULT NULL,
  `is_driver_assigned_key` int DEFAULT NULL,
  `cancellations_time_in_seconds` text
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;
```

## Analysis

Once the tables were created in Mysql, did analysis to respond to the main questions. Utilized MySQL and Google Sheets to create visualizations.

## Q1: Build up distribution of orders according to reasons for failure: cancellations before and after driver assignment, and reasons for order rejection. Analyse the resulting plot. Which category has the highest number of orders?
 
```sql
-- get counts of cancellations by both categories: order_status_key , and is_driver_assigned_key
select 
	order_gk as order_id,
    case when order_status_key = 4 then 'client' else 'system' end as cancel_status, 
    case when is_driver_assigned_key  = 1 then 'yes' else 'no' end as driver_assigned
from training.gett_orders;
``` 
<br/>  
Returned a list of ids with the cancel status and driver status, then created a pivot table in Google Sheets:  

<img width="840" alt="image" src="https://github.com/user-attachments/assets/7b2aaf07-1a8c-4799-9952-977e445549c5">
<br/>

**Answers/Insights:** 
* Percentage of cancellation once a driver is assigned = 26% of the cancellations happen when a driver was already assigned, regardless of system vs client cancellation.

<br/>

## Q2: Plot the distribution of failed orders by hours. Is there a trend that certain hours have an abnormally high proportion of one category or another? What hours are the biggest fails? How can this be explained?<br/>

```sql
SELECT 
    HOUR(order_datetime) AS hour_of_day,
    SUM(CASE WHEN order_status_key = 4 THEN 1 ELSE 0 END) AS client_counts,
    SUM(CASE WHEN order_status_key = 9 THEN 1 ELSE 0 END) AS system_counts
FROM training.gett_orders
GROUP BY HOUR(order_datetime) ORDER BY hour_of_day ASC;
```
<br/>  
Returned a list of the counts of cancellations (clients, systems) per hour of the day, then created a line chart in Google Sheets to visualize the distribution trends:  

<img width="972" alt="image" src="https://github.com/user-attachments/assets/2869d9c4-1cce-45dd-a690-0f7a4e614019">
<br/>

**Answers/Insights:** 
* 8:00am specifically has an abnormal peak. 7:00am-9:00am, and 8pm-11pm are the ranges where we can see most of the cancellations.
* This could be explained due to those rush hours being when workers call drivers to take them to and from work, and potentially after work dinner and back home/hotel.


