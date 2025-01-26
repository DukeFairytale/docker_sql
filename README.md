# Module 1 Homework: Docker & SQL

## Question 1. Understanding docker first run 

- 24.3.1


## Question 2. Understanding Docker networking and docker-compose

- postgres:5433


## Question 3. Trip Segmentation Count

SELECT
    CASE
        WHEN trip_distance <= 1.0 THEN '1m>' 
        WHEN trip_distance > 1.0 AND trip_distance <= 3.0 THEN '1-3m'
        WHEN trip_distance > 3.0 AND trip_distance <= 7.0 THEN '3-7m'
        WHEN trip_distance > 7.0 AND trip_distance <= 10.0 THEN '7-10m'
        WHEN trip_distance > 10.0 THEN '10m<'
        ELSE 'empty_data'
    END AS miles_group
    ,COUNT(*) AS drives_cnt
FROM public.green_taxi
WHERE
    lpep_pickup_datetime::DATE BETWEEN '2019-10-01' AND '2019-10-31'
    AND lpep_dropoff_datetime::DATE BETWEEN '2019-10-01' AND '2019-10-31'
GROUP BY miles_group
ORDER BY miles_group

## Question 4. Longest trip for each day

SELECT lpep_pickup_datetime::DATE
FROM public.green_taxi
WHERE lpep_pickup_datetime::DATE IN ('2019-10-11', '2019-10-24', '2019-10-26', '2019-10-31')
GROUP BY lpep_pickup_datetime::DATE
ORDER BY MAX(trip_distance) DESC
LIMIT 1;

## Question 5. Three biggest pickup zones

SELECT tzl."Zone", SUM(td.total_amount) AS total_amount_sum
FROM public.green_taxi AS td
INNER JOIN public.zones AS tzl
ON td."PULocationID" = tzl."LocationID"
WHERE td.lpep_pickup_datetime::DATE = '2019-10-18'
GROUP BY tzl."Zone"
HAVING SUM(td.total_amount) > 13000
ORDER BY total_amount_sum DESC
LIMIT 3;


## Question 6. Largest tip

SELECT do_tzl."Zone"
FROM public.green_taxi AS td
INNER JOIN public.zones AS pu_tzl ON td."PULocationID" = pu_tzl."LocationID"
INNER JOIN public.zones AS do_tzl ON td."DOLocationID" = do_tzl."LocationID"
WHERE TO_CHAR(td.lpep_pickup_datetime, 'YYYY-MM') = '2019-10'
  AND pu_tzl."Zone" = 'East Harlem North'
ORDER BY td.tip_amount DESC
LIMIT 1;
