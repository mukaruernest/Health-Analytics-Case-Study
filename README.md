# Health Analytics Case Study

## Table of Contents 

- [Business Questions]() 
- [Datasets]()
- [Solution]()

## Business Questions

1. How many unique users exist in the logs dataset?
2. How many total measurements do we have per user on average?
3. What about the median number of measurements per user?
4. How many users have 3 or more measurements?
5. How many users have 1,000 or more measurements?
6. Have logged blood glucose measurements?
7. Have at least 2 types of measurements?
8. Have all 3 measures - blood glucose, weight and blood pressure?
9. What is the median systolic/diastolic blood pressure values?

Before anwering the Business questions I needed to see the dataset I will be dealing with. Below is the `health.user_logs` table
```SQL
SELECT * 
FROM health.user_logs
```

## Dataset
_health.user_logs_
![image](https://user-images.githubusercontent.com/10958742/124757071-e26d3280-df35-11eb-9f21-b023a46556bd.png)

## Solutions

1. How many unique users exist in the logs dataset?
```SQL
SELECT COUNT(DISTINCT id)
FROM health.user_logs;
```
| count |
|-------|
| 554   |

**For questions 2-8 we created a temporary table**
```SQL
DROP TABLE IF EXISTS user_measure_count;
CREATE TEMP TABLE user_measure_count AS 
SELECT
    id,
    COUNT(*) AS measure_count,
    COUNT(DISTINCT measure) as unique_measures
  FROM health.user_logs
  GROUP BY 1;
```
2. How many total measurements do we have per user on average?
```SQL
SELECT
  ROUND(AVG(measure_count), 2)
FROM user_measure_count;
```
| round |
|-------|
| 79.23 |

3. What about the median number of measurements per user?
```SQL
SELECT 
   PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY measure_count) AS median_value 
FROM user_measure_count;
```
| median_value |
|--------------|
|       2      |

4. How many users have 3 or more measurements?
```SQL
SELECT
  COUNT(*)
FROM user_measure_count
WHERE measure_count >= 3;
```
| count |
|-------|
|  209   |

5. How many users have 1,000 or more measurements?
```SQL
SELECT
  COUNT(id)
FROM user_measure_count
WHERE measure_count >= 1000;
```
| count |
|-------|
| 5     |

6. Have logged blood glucose measurements?
```SQL
SELECT
  COUNT(DISTINCT id)
FROM health.user_logs
WHERE measure = 'blood_glucose';
```
| count |
|-------|
|  325  |

7. Have at least 2 types of measurements?
```SQL
SELECT 
  COUNT(*)
FROM user_measure_count
WHERE unique_measures >= 2;
```
| count |
|-------|
| 204   |

8. Have all 3 measures - blood glucose, weight and blood pressure?
```SQL
SELECT
  COUNT(*)
FROM user_measure_count
WHERE unique_measures = 3;
```
| count |
|-------|
|   50  |

9. What is the median systolic/diastolic blood pressure values?
```SQL
SELECT
  PERCENTILE_CONT(0.5) WITHIN GROUP(ORDER BY systolic) AS median_systolic,
  PERCENTILE_CONT(0.5) WITHIN GROUP(ORDER BY diastolic) AS median_diastolic
FROM health.user_logs
WHERE measure = 'blood_pressure';
```
| median_systolic | median_diastolic |
|:---------------:|:----------------:|
|       126       |        79        |

