# Layoff Insights
Performing wrangling and exploring data analysis on global laid off trend across various industries around the world.

## Solutions

### 1. What is the highest number and percentage of laid off in a day?
~~~~sql
SELECT MAX(total_laid_off) as 'Maximum Amount of Total Laid Off', MAX(percentage_laid_off) as 'Maximum Laid Off Percentage'
FROM layoff_staging2;
~~~~

#### Answer: 

### 2. What is the start date and end date of laid off data?
~~~~sql
SELECT MIN(`date`) as 'Start Date', MAX(`date`) as 'End Date'
FROM layoff_staging2;
~~~~

#### Answer: 

### 3. What is the top 5 companies that have the highest number of laid off?
~~~~sql
SELECT company, SUM(total_laid_off) as 'Total Laid Off'
FROM layoff_staging2
GROUP BY company
ORDER BY 2 DESC
LIMIT 5;
~~~~

#### Answer: 

### 4. What is the top 5 industrial that have the highest number of laid off?
~~~~sql
SELECT industry, SUM(total_laid_off) as 'Total Laid Off'
FROM layoff_staging2
GROUP BY industry
ORDER BY 2 DESC
LIMIT 5;
~~~~

#### Answer: 

### 5. What is the top 5 countries that have the highest number of laid off?
~~~~sql
SELECT country, SUM(total_laid_off) as 'Total Laid Off'
FROM layoff_staging2
GROUP BY country
ORDER BY 2 DESC
LIMIT 5;
~~~~

#### Answer: 

### 6. What year has the highest laid off?
~~~~sql
-- CHECK WHICH YEAR HAS THE HIGHEST LAID OFF
SELECT YEAR(`date`) as 'Year', SUM(total_laid_off) as 'Total Laid Off'
FROM layoff_staging2
GROUP BY Year
HAVING Year IS NOT NULL
ORDER BY Year DESC;
~~~~

#### Answer: 

### 7. What month has the highest laid off?
~~~~sql
SELECT SUBSTRING(`date`, 6, 2) as `MONTH`, SUM(total_laid_off) as 'Total Laid Off'
FROM layoff_staging2
GROUP BY MONTH
HAVING MONTH IS NOT NULL
ORDER BY 1;
~~~~

#### Answer: 

### 8. Calculate monthly layoffs and their cumulative rolling sum over time.
~~~~sql
WITH rolling_total AS (
SELECT SUBSTRING(`date`, 1, 7) as `MONTH`, SUM(total_laid_off) as total_off
FROM layoff_staging2
WHERE SUBSTRING(`date`, 1, 7) IS NOT NULL 
GROUP BY `MONTH` 
ORDER BY 1 ASC
)
SELECT `MONTH`, total_off,SUM(total_off) OVER(ORDER BY `MONTH`) as rolling_sum FROM rolling_total;
~~~~

#### Answer: 

### 9. What year is the highest laid off in top the 5 company?
~~~~sql
SELECT company, YEAR(`date`) as YEAR, SUM(total_laid_off) as 'Total Laid Off'
FROM layoff_staging2
GROUP BY company, YEAR(`date`)
ORDER BY 3 DESC
LIMIT 5;
~~~~

#### Answer: 

### 10. Find the top 5 companies with the highest layoffs per year, ranked by total layoffs.
~~~~sql
WITH Company_Year (company, year, total_laid_off) AS (
SELECT company, YEAR(`date`), SUM(total_laid_off) FROM layoff_staging2 GROUP BY company, YEAR(`date`) 
), Company_Ranking AS (
SELECT *, DENSE_RANK() OVER(PARTITION BY year ORDER BY total_laid_off DESC) AS RANKING FROM Company_Year WHERE year IS NOT NULL ORDER BY RANKING)
SELECT * FROM Company_Ranking WHERE RANKING <= 5 ORDER BY year
;
~~~~

#### Answer: 
