# Insights from Failed Orders
Project from: https://platform.stratascratch.com/data-projects/insights-failed-orders

_Gett, previously known as GetTaxi, is an Israeli-developed technology platform solely focused on corporate Ground Transportation Management (GTM). They have an application where clients can order taxis, and drivers can accept their rides (offers). At the moment, when the client clicks the Order button in the application, the matching system searches for the most relevant drivers and offers them the order. In this task, we would like to investigate some matching metrics for orders that did not completed successfully, i.e., the customer didn't end up getting a car._

Notes
* order = when a client requests a taxis
* offer = when a driver accepts a ride

## Assignment
- Q1: Build up distribution of orders according to reasons for failure: cancellations before and after driver assignment, and reasons for order rejection. Analyse the resulting plot. Which category has the highest number of orders?
- Q2: Plot the distribution of failed orders by hours. Is there a trend that certain hours have an abnormally high proportion of one category or another? What hours are the biggest fails? How can this be explained?
- Q3: Plot the average time to cancellation with and without driver, by the hour. If there are any outliers in the data, it would be better to remove them. Can we draw any conclusions from this plot?
- Q4: Plot the distribution of average ETA by hours. How can this plot be explained?
- BONUS Hexagons. Using the h3 and folium packages, calculate how many sizes 8 hexes contain 80% of all orders from the original data sets and visualise the hexes, colouring them by the number of fails on the map.

## Data Description
We have two data sets: data_orders and data_offers, both being stored in a CSV format. 

The **data_orders** data set contains the following columns:
- order_datetime - time of the order
- origin_longitude - longitude of the order
- origin_latitude - latitude of the order
- m_order_eta - time before order arrival
- order_gk - order number
- order_status_key - status, an enumeration consisting of the following mapping:
    - 4 - cancelled by client,
    - 9 - cancelled by system, i.e., a reject
- is_driver_assigned_key - whether a driver has been assigned
- cancellation_time_in_seconds - how many seconds passed before cancellation

The **data_offers** data set is a simple map with 2 columns:
- order_gk - order number, associated with the same column from the orders data set
- offer_id - ID of an offer

Practicalities: Make sure that the solution reflects your entire thought process including the preparation of data - it is more important how the code is structured rather than just the final result or plot.



----------------------------------------------------------------
----------------------------------------------------------------
## SQL Create statements

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

----------------------------------------------------------------
----------------------------------------------------------------
## Analysis

**Q1: Build up distribution of orders according to reasons for failure: cancellations before and after driver assignment, and reasons for order rejection. Analyse the resulting plot. Which category has the highest number of orders?**

Ran query: 
```sql
-- get counts of cancellations by both categories: order_status_key , and is_driver_assigned_key
select 
	order_gk as order_id,
    case when order_status_key = 4 then 'client' else 'system' end as cancel_status, 
    case when is_driver_assigned_key  = 1 then 'yes' else 'no' end as driver_assigned
from training.gett_orders;
```

Returned a list of ids with the cancel status and driver status, then created a pivot table in Google Sheets:

<img width="720" alt="image" src="https://github.com/user-attachments/assets/59b02b5d-1f99-4f4e-957e-aa75e2d3c424">

Answers/Insights: 
* Percentage of cancellation once a driver is assigned =
* Percentage of cancellation before a driver is assigned = 
* we can see that when a driver is assigned, there is almost 0% chance that the ride is cancelled by the system. 


**Q2: Plot the distribution of failed orders by hours. Is there a trend that certain hours have an abnormally high proportion of one category or another? What hours are the biggest fails? How can this be explained?**
...

**Q3: Plot the average time to cancellation with and without driver, by the hour. If there are any outliers in the data, it would be better to remove them. Can we draw any conclusions from this plot?**
...

**Q4: Plot the distribution of average ETA by hours. How can this plot be explained?**
...
