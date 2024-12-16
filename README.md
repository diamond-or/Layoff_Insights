# Layoff Insights
Performing wrangling and exploring data analysis on global laid off trend across various industries around the world.

## Solutions

### 1. What is the highest number and percentage of laid off in a day?
~~~~sql
SELECT MAX(total_laid_off) as 'Maximum Amount of Total Laid Off', MAX(percentage_laid_off) as 'Maximum Laid Off Percentage'
FROM layoff_staging2;
~~~~

#### Answer: 
<img width="366" alt="Screenshot 2024-12-16 at 3 49 45 pm" src="https://github.com/user-attachments/assets/9d9f0bda-2636-4113-b13a-2cf5528fd02b" />

### 2. What is the start date and end date of laid off data?
~~~~sql
SELECT MIN(`date`) as 'Start Date', MAX(`date`) as 'End Date'
FROM layoff_staging2;
~~~~

#### Answer: 
<img width="195" alt="Screenshot 2024-12-16 at 3 51 14 pm" src="https://github.com/user-attachments/assets/ff3a41e2-6f13-4da8-a991-1dd5df68d389" />

### 3. What is the top 5 companies that have the highest number of laid off?
~~~~sql
SELECT company, SUM(total_laid_off) as 'Total Laid Off'
FROM layoff_staging2
GROUP BY company
ORDER BY 2 DESC
LIMIT 5;
~~~~

#### Answer: 
<img width="197" alt="Screenshot 2024-12-16 at 3 54 21 pm" src="https://github.com/user-attachments/assets/7751c260-23ba-497a-8347-0001b0dc609e" />

### 4. What is the top 5 industrial that have the highest number of laid off?
~~~~sql
SELECT industry, SUM(total_laid_off) as 'Total Laid Off'
FROM layoff_staging2
GROUP BY industry
ORDER BY 2 DESC
LIMIT 5;
~~~~

#### Answer: 
<img width="200" alt="Screenshot 2024-12-16 at 4 51 31 pm" src="https://github.com/user-attachments/assets/849e1214-b829-483f-aba6-21e1678b7a21" />

### 5. What is the top 5 countries that have the highest number of laid off?
~~~~sql
SELECT country, SUM(total_laid_off) as 'Total Laid Off'
FROM layoff_staging2
GROUP BY country
ORDER BY 2 DESC
LIMIT 5;
~~~~

#### Answer: 
<img width="373" alt="Screenshot 2024-12-16 at 4 51 40 pm" src="https://github.com/user-attachments/assets/c21e04b0-8cc5-4904-9b3a-61bb15533008" />

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
<img width="121" alt="Screenshot 2024-12-16 at 4 52 04 pm" src="https://github.com/user-attachments/assets/19a5b56c-5d0c-42b2-8be1-e6064b4b65eb" />

### 7. What month has the highest laid off?
~~~~sql
SELECT SUBSTRING(`date`, 6, 2) as `MONTH`, SUM(total_laid_off) as 'Total Laid Off'
FROM layoff_staging2
GROUP BY MONTH
HAVING MONTH IS NOT NULL
ORDER BY 1;
~~~~

#### Answer: 
<img width="131" alt="Screenshot 2024-12-16 at 4 52 32 pm" src="https://github.com/user-attachments/assets/75fc1fed-b2a6-43c1-bb6a-c0a61f12caea" />

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
<img width="191" alt="Screenshot 2024-12-16 at 4 52 58 pm" src="https://github.com/user-attachments/assets/a8fff57f-d2bf-4494-b839-9b84fd3bf120" />

### 9. What year is the highest laid off in top the 5 company?
~~~~sql
SELECT company, YEAR(`date`) as YEAR, SUM(total_laid_off) as 'Total Laid Off'
FROM layoff_staging2
GROUP BY company, YEAR(`date`)
ORDER BY 3 DESC
LIMIT 5;
~~~~

#### Answer: 
<img width="174" alt="Screenshot 2024-12-16 at 4 53 10 pm" src="https://github.com/user-attachments/assets/5b65a84b-5975-4c44-9fa4-d67318795bf9" />

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
<img width="252" alt="Screenshot 2024-12-16 at 4 53 24 pm" src="https://github.com/user-attachments/assets/fc2ffd2d-bf86-4ef9-918a-a4f31041db19" />
