# Covid19_Research_BigQuery

This is the google cloud quest challenge SQL codes  i used

TASK 1

select 
      sum(cumulative_confirmed) AS total_cases_worldwide


from  bigquery-public-data.covid19_open_data.covid19_open_data

where date ='2020-05-25';




--------------------------------------------------------------------

TASK 2

WITH death_by_states AS

(SELECT
      sum(cumulative_deceased) AS death_count
	  subregion1_name AS state
FROM  
bigquery-public-data.covid19_open_data.covid19_open_data

WHERE  country_name='United States of America'
AND    subregion1_name IS NOT NULL
AND    date=''

GROUP BY subregion1_name

)
	  



SELECT
      COUNT(Subregion1_name) AS count_of_states
	  
FROM    death_by_states


WHERE death_count >.....


;

-------------------------------------------------

TASK 3

SELECT * FROM

(SELECT subregion1_name AS state
       SUM(cumulative_confirmed) AS total_confirmed_cases
	   
FROM
bigquery-public-data.covid19_open_data.covid19_open_data


WHERE country_code='US'
AND   date=''
AND   subregion1_name IS NOT NULL

GROUP BY subregion1_name
ORDER BY total_confirmed_cases desc
)

WHERE  

total_confirmed_cases >......

;

------------------------------------------------

TASK 4

SELECT
      SUM(cumulative_confirmed) AS total_confirmed_cases,
	  SUM(cumulative_deceased) AS total_deaths,
	  ((SUM(cumulative_deceased))/(SUM(cumulative_confirmed))*100) AS case_fatality_ratio
	  
FROM
bigquery-public-data.covid19_open_data.covid19_open_data


WHERE country_name='Italy'
AND   date BETWEEN '' AND ''

;

-------------------------------------------------

TASK 5

SELECT date

	   
FROM  
bigquery-public-data.covid19_open_data.covid19_open_data

WHERE  country_name='Italy'
AND cumulative_deceased>......

ORDER BY date asc
LIMIT 1
;





-----------------------------------------------

TASK 6


WITH india_cases_by_date AS (
  SELECT
    date,
    SUM(cumulative_confirmed) AS cases
  FROM
    `bigquery-public-data.covid19_open_data.covid19_open_data`
  WHERE
    country_name="India"
    AND date between '2020-02-21' and '2020-03-15'
  GROUP BY
    date
  ORDER BY
    date ASC
 )
, india_previous_day_comparison AS
(SELECT
  date,
  cases,
  LAG(cases) OVER(ORDER BY date) AS previous_day,
  cases - LAG(cases) OVER(ORDER BY date) AS net_new_cases
FROM india_cases_by_date
)

SELECT COUNT(*)
FROM 
india_previous_day_comparison
WHERE net_new_cases=0



----------------------------------------------

TASK 7


WITH us_cases_by_date AS (
  SELECT
    date,
    SUM(cumulative_confirmed) AS cases
  FROM
    `bigquery-public-data.covid19_open_data.covid19_open_data`
  WHERE
    country_name="United States of America"
    AND date between '2020-02-21' and '2020-03-15'
  GROUP BY
    date
  ORDER BY
    date ASC
 )
, us_previous_day_comparison AS
(SELECT
  date,
  cases,
  LAG(cases) OVER(ORDER BY date) AS previous_day,
  cases - LAG(cases) OVER(ORDER BY date) AS net_new_cases,
  (cases - LAG(cases) OVER(ORDER BY date))*100/LAG(cases) OVER(ORDER BY date) AS percentage_increase
FROM us_cases_by_date
)

SELECT date,
       cases as Confirmed_Cases_On_Day,
	   previous_day as Confirmed_Cases_Previous_Day,
	   percentage_increase as Percentage_Increase_In_Cases
	   
FROM   us_previous_day_comparison

WHERE  percentage_increase>....

;








-----------------------------------------------

TASK 8

WITH cases_by_country AS

(
SELECT 
      country_name AS country,
	  SUM(cumulative_confirmed) AS cases,
	  SUM(cumulative_recovered) AS recovered_cases
	  
FROM 
`bigquery-public-data.covid19_open_data.covid19_open_data`

WHERE 
     date=''
	 
GROUP BY 

    country_name
	
),

recovered_rate AS

(
   SELECT 
         country,cases,recovered_cases,
		 (recovered_cases *100)/cases  AS recovery_rate
		 
	FROM  cases_by_country

)


SELECT
      country,cases AS confirmed_cases,
	  recovered_cases,
	  recovery_rate
	  
FROM  recovered_rate

WHERE cases > 50000

ORDER BY recovery_rate desc
LIMIT 20

;



----------------------------------------------

TASK 9




WITH
  france_cases AS (
  SELECT
    date,
    SUM(cumulative_confirmed) AS total_cases
  FROM
    `bigquery-public-data.covid19_open_data.covid19_open_data`
  WHERE
    country_name="France"
    AND date IN ('2020-01-24',
      '2020-04-15')
  GROUP BY
    date
  ORDER BY
    date)
, summary as (
SELECT
  total_cases AS first_day_cases,
  LEAD(total_cases) OVER(ORDER BY date) AS last_day_cases,
  DATE_DIFF(LEAD(date) OVER(ORDER BY date),date, day) AS days_diff
FROM
  france_cases
LIMIT 1
)
select first_day_cases, last_day_cases, days_diff, POWER((last_day_cases/first_day_cases),(1/days_diff))-1 as cdgr
from summary

;


-----------------------------------------------------

TASK 10

SELECT date,
       SUM(cumulative_confirmed) AS Number_of_Confirmed_Cases,
	   SUM(cumulative_deceased) AS Number_of_Deaths
	   
FROM 
`bigquery-public-data.covid19_open_data.covid19_open_data`

WHERE 
     date BETWEEN '' AND ''

AND  country_name='United States of America'

GROUP BY date

;







	  
