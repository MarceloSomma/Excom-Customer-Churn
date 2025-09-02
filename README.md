# Data Cleaning and Preparation

The goal of this process was to identify and resolve data quality issues, including inconsistencies, missing values, and outliers, to ensure the data's integrity and reliability for subsequent analysis.

## Demographics Table 📊

#### Issue: 
The gender column contained inconsistent spelling and capitalization (e.g., 'Male', 'Female', 'male', 'female', 'Ma', 'F', 'M').
#### Solution:
To ensure uniformity, all values were standardized to either 'Male' or 'Female'.
```sql
    UPDATE demographics
    SET gender = 'Male'
    WHERE gender IN ('Ma', 'male', 'M');

    UPDATE demographics
    SET gender = 'Female'
    WHERE gender IN ('female', 'F');
```
#### Issue:
The age column contained null values.
#### Solution:
Null values were retained to preserve the original data. They will be addressed during analysis by labeling them as 'Unknown' for visualization purposes.
  
#### Issue:
The n_dependents column contained outlier values (e.g., 50, 999) that were clearly data entry errors.
#### Solution:
After consulting with stakeholders, a realistic maximum value of 9 was established. Values exceeding this cap were updated to 9.
```sql
    UPDATE demographics
    SET n_dependents = CASE
    WHEN n_dependents > 9 THEN 9
    ELSE n_dependents
    END;
```
## Location Table 📍
#### Issue:
The country column included irrelevant values, such as 'Mexico', which are outside the scope of a US-based company's analysis.
#### Solution:
To focus on US customers while retaining a record of out-of-region data, these values were replaced with 'Out-of-region'.
```sql
    UPDATE location
    SET country = CASE
    WHEN country != 'United States' THEN 'Out-of-region'
    ELSE country
    END;
```
#### Issue:
The longitude and latitude columns contained null values.
#### Solution:
These null values were kept intact, as they can be easily filtered out during the analysis and visualization phases.

## Population Table 🗺️
#### Issue:
The population column contained extreme outliers, with values as low as 2 and as high as 50,000,000, which are inconsistent with the dataset's origin in California.
#### Solution:
Based on research and stakeholder consultation, a realistic population range was defined, from 200 (Amador City) to 4,000,000 (Los Angeles). Values outside this range were capped to normalize the data.
```sql
    UPDATE population
    SET population = CASE
    WHEN population < 200 THEN 200
    WHEN population > 4000000 THEN 4000000
    ELSE population
    END;
```
## Services Table 🛠️
#### Issue:
The internet_service column contained the invalid value 'Maybe', while the expected values were 'Yes' or 'No'.
#### Solution:
The 'Maybe' values were reclassified as 'Unknown' to maintain data consistency.
```sql
    UPDATE services
    SET internet_service = CASE
    WHEN internet_service = 'Maybe' THEN 'Unknown'
    ELSE internet_service
    END;
```
#### Issue:
The internet_type column contained null values.
#### Solution:
Null values were replaced with 'Unknown' to improve data integrity and clarity.
```sql
    UPDATE services
    SET internet_type = CASE
    WHEN internet_type IS NULL THEN 'Unknown'
    ELSE internet_type
    END;
```
#### Issue:
The column name device_protecion was misspelled.
#### Solution:
The column was renamed to device_protection to correct the spelling.
```sql
    ALTER TABLE services
    RENAME COLUMN device_protecion TO device_protection;
```
#### Issue:
The unlimited_data column contained invalid '---' values.
#### Solution:
These invalid values were replaced with 'Unknown' to ensure data integrity.
```sql
    UPDATE services
    SET unlimited_data = CASE
    WHEN unlimited_data = '---' THEN 'Unknown'
    ELSE unlimited_data
    END;
```
#### Issue:
The monthly_charge column included an extreme outlier of $99,999.
#### Solution:
A realistic range for monthly charges, from $18.25 to $130, was established based on company plan data. Outlier values were capped at these thresholds.
```sql
    UPDATE services
    SET monthly_charge = CASE
    WHEN monthly_charge > 130 THEN 130
    WHEN monthly_charge < 18.25 THEN 18.25
    ELSE monthly_charge
    END;
```
## Status Table 📈
#### Issue:
The customer_id column, which serves as a primary key, contained null values.
#### Solution:
Records with a null customer_id were deleted, as they cannot be uniquely identified.
```sql
    DELETE FROM status
    WHERE customer_id IS NULL;
```
#### Issue:
The satisfaction_score column contained null values.
#### Solution:
The null values were left untouched to maintain the original data. They will be handled during analysis by labeling them as 'Unknown'.
#### Issue:
The churn_category and churn_reason columns contained null values.
#### Solution:
Null values in churn_category were replaced with 'Unknown'. For churn_reason, null values were replaced with the existing 'Don't know' value to maintain consistency.
```sql 
    UPDATE status
    SET churn_reason = CASE
    WHEN churn_reason IS NULL THEN 'Don''t know'
    ELSE churn_reason
    END
```


