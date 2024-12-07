# Lay_offs---Data-Cleaning-With-SQL
This project demonstrates how SQL is used for cleaning and preparing raw data for analysis. The dataset focuses on employee layoffs and contains inconsistencies and missing values, which are addressed through structured SQL queries.

Handling missing and inconsistent data.
Removing duplicates.
Standardizing formats for accuracy.
Files
lay_offs_cleaning.sql: SQL script for the cleaning process.
lay_offs_raw.csv: Original dataset with inconsistencies.
Outcome
A refined, analysis-ready dataset that provides clear insights into layoff trends across industries.


#OBJECTIVES
	#REMOVE DUPLICATE
  #STANDARDIZE THE DATA 
  #NULL VALUES OR BALNK VALUES
  #REMOVE ANY COLUMNS OR ROWS

#DATA CLEANING
SELECT * FROM layoffs;

CREATE TABLE layoffs_staging
LIKE layoffs;

SELECT * 
FROM layoffs_staging;

INSERT layoffs_staging
SELECT *
FROM layoffs;

#REMOVING DUPLICATE

SELECT *,
ROW_NUMBER() OVER(
PARTITION BY company, industry, total_laid_off, percentage_laid_off,  date) AS row_num
FROM layoffs_staging;

WITH duplicate_cte AS
(
SELECT *,
ROW_NUMBER() OVER(
PARTITION BY company, location, industry, total_laid_off, percentage_laid_off,  date, 
stage, country, funds_raised_millions ) AS row_num
FROM layoffs_staging
)
SELECT* 
FROM duplicate_cte
WHERE row_num > 1;

SELECT *
FROM layoffs_staging
WHERE company ='casper';






CREATE TABLE `layoffs_staging2` (
  `company` text,
  `location` text,
  `industry` text,
  `total_laid_off` int DEFAULT NULL,
  `percentage_laid_off` text,
  `date` text,
  `stage` text,
  `country` text,
  `funds_raised_millions` int DEFAULT NULL,
   `row_num` INT
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;

SELECT * FROM layoffs_staging2;

INSERT INTO layoffs_staging2
SELECT *,
ROW_NUMBER() OVER(
PARTITION BY company, location, industry, total_laid_off, percentage_laid_off,  date,
stage, country, funds_raised_millions ) AS row_num
FROM layoffs_staging;

SELECT * 
FROM layoffs_staging2
WHERE row_num > 1;

DELETE 
FROM layoffs_staging2
WHERE row_num > 1;

SELECT * 
FROM layoffs_staging2;


#STANDARDIZING DATA

SELECT company, TRIM(company)
FROM layoffs_staging2;

UPDATE layoffs_staging2
SET comapany = TRIM(company);

SELECT *
FROM layoffs_staging2
WHERE  industry LIKE 'crypto%';

UPDATE layoffs_staging2
SET industry= 'crypto'
WHERE industry LIKE 'crypto5';

SELECT* FROM layoffs_staging2;

SELECT DISTINCT country
FROM layoffs_staging2
ORDER BY 1;

SELECT DISTICT country, TRIM( TRAILING '.' FROM country)
FROM layoffs_staging2
ORDER BY 1;

UPDATE layoffs_staging2
SET country = TRIM( TRAILING '.' FROM country)
WHERE country LIKE 'united states%';

SELECT `date`, 
STR_TO_DATE('date', '%m/%d/%Y')
FROM layoffs_staging2;

UPDATE layoffs_staging2
SET  date = STR_TO_DATE(`date`, '%m/%d/%Y');

SELECT `date` 
FROM layoffs_staging2;

ALTER TABLE layoffs_staging2
MODIFY COLUMN `date` DATE;

SELECT * FROM 
layoffs_staging2
WHERE total_laid_off IS NULL;


SELECT *
FROM layoffs_staging2
WHERE company = 'Airbnb';

UPDATE layoffs_staging2
SET industry = NULL
WHERE industry = '';

SELECT *
FROM layoffs_staging2 AS t1
JOIN layoffs_staging2 AS t2
	ON t1.company= t2.company
WHERE t1. industry is Null
AND t2. industry is NOT NULL;

UPDATE layoffs_staging2 AS t1
JOIN layoffs_staging2 AS t2
	ON t1.company= t2.company
SET t1.industry=t2.industry
WHERE t1.industry is NULL
AND t2.industry IS NOT NULL;


DELETE 
FROM layoffs_staging2
WHERE total_laid_off IS NULL 
AND percentage_laid_off IS NULL;

ALTER TABLE layoffs_staging2
DROP COLUMN row_num;

END$$
DELIMITER ;














































































