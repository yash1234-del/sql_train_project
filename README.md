# UK-TRAIN-RIDES

### create tables in postgre sql

CREATE TABLE tr(
	Transaction_ID VARCHAR,
    Date_of_Purchase DATE,
	Time_of_Purchase TIME,
	Purchase_Type TEXT,
	Payment_Method TEXT,
	Railcard TEXT,
	Ticket_Class TEXT,
	Ticket_Type TEXT,
	Price INT,
	Departure_Station TEXT,
	Arrival_Destination TEXT,
	Date_of_Journey DATE,
	Departure_Time TIME,
	Arrival_Time TIME,
	Actual_Arrival_Time TIME,
	Journey_Status TEXT,
	Reason_for_Delay TEXT,
	Refund_Request TEXT
)

## Q1 What are the most popoular routes?

WITH NEW_D AS (
	SELECT transaction_id,CONCAT(departure_station,' to ',arrival_destination) as routes
	FROM tr)

select routes ,
	rank () over (order by count(routes)) as ranking ,
	count(transaction_id)
	from NEW_D
	GROUP BY routes
	order by ranking desc

## Q2 What are the peak travel times?

with nt as (
	select extract(hour from (departure_time)) as d_t
	from tr
)
	select d_t,
rank() over(order by count(d_t)) as ranking
from nt
group by d_t
order by ranking desc

## Q3 How does revenue vary by ticket type and class?

select ticket_class,ticket_type , sum(price) as revenue
from tr
group by ticket_class,ticket_type
order by revenue desc

## Q4 What is the on time performance? What are the main contribuiting factors

SELECT journey_status,count(journey_status) as total 
from tr
group by journey_status
order by total desc

## Q5 What are the main contribuiting factors?

select reason_for_delay, count(reason_for_delay) as number_of_delay
from tr
group by reason_for_delay
order by number_of_delay desc
