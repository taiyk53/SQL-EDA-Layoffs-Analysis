# SQL-EDA-Layoffs-Analysis
Exploratory Data Analysis (EDA) on Layoffs Dataset  

Project Overview
This project explores layoff trends using SQL, analyzing the impact of layoffs across different industries, companies, and time periods. The dataset used contains information on layoffs, including the number of employees laid off, the total workforce, industry, country, company stage, and funding raised.

Project Objectives
•	Identify companies and industries most affected by layoffs.
•	Examine layoff trends over time using time series analysis.
•	Rank the most affected companies and industries.
•	Analyze which years and months had the highest layoffs.

Dataset Used
https://github.com/taiyk53/SQL-Layoffs-Data-Cleaning/blob/main/layoffs.csv

Key SQL Queries and Analysis

**1 General Overview**
•	Fetching all data from the dataset: 
	SELECT * FROM layoffs_staging2;
 
•Finding the maximum number of employees laid off and the highest layoff percentage: 
 SELECT max(total_laid_off), max(percentage_laid_off) FROM layoffs_staging2;

**2 Companies with 100% Layoffs**
•	Listing companies that laid off all their employees (100% layoffs): 
	SELECT * FROM layoffs_staging2 WHERE percentage_laid_off = 1 ORDER BY total_laid_off DESC;

**3 Industry and Country Analysis**
•	Industries with the highest layoffs: 
	SELECT industry, sum(total_laid_off) FROM layoffs_staging2 GROUP BY industry ORDER BY 2 DESC;
 
•	Countries with the highest layoffs: 
	SELECT country, sum(total_laid_off) FROM layoffs_staging2 GROUP BY country ORDER BY 2 DESC;

**4 Time Series Analysis**
•	Monthly layoff trends: 
 SELECT substring(`date`, 1,7) AS `MONTH`, sum(total_laid_off) FROM layoffs_staging2 WHERE substring(`date`, 1,7) IS NOT NULL GROUP BY `MONTH` ORDER BY 1 ASC;

•	Yearly layoff trends: 
	SELECT YEAR(`date`), sum(total_laid_off) FROM layoffs_staging2 GROUP BY YEAR(`date`) ORDER BY 1 DESC;

** 5 Rolling Total Layoffs**
•	Calculating the cumulative layoffs over time: 
	WITH Rolling_Total AS (
   SELECT substring(`date`, 1,7) AS `MONTH`, sum(total_laid_off) AS total_off
	  FROM layoffs_staging2
	  WHERE substring(`date`, 1,7) IS NOT NULL
	  GROUP BY `MONTH`
	  ORDER BY 1 ASC
	)
	SELECT `MONTH`, total_off, SUM(total_off) OVER(ORDER BY `MONTH`) AS rolling_total FROM Rolling_Total;

** 6 Companies with the Most Layoffs per Year**
•	Ranking companies based on yearly layoffs: 
	WITH Company_Year AS (
	  SELECT company, year(`date`) AS years, SUM(total_laid_off) AS total_laid_off
	  FROM layoffs_staging2
	  GROUP BY company, year(`date`)
	)
	SELECT *, DENSE_RANK() OVER(PARTITION BY years ORDER BY total_laid_off DESC) AS Ranking FROM Company_Year WHERE years IS NOT NULL ORDER BY Ranking ASC;

•	Filtering the top 5 companies per year: 
	WITH Company_Year AS (
	  SELECT company, year(`date`) AS years, SUM(total_laid_off) AS total_laid_off
	  FROM layoffs_staging2
	  GROUP BY company, year(`date`)
	), Company_Year_Rank AS (
	  SELECT *, DENSE_RANK() OVER(PARTITION BY years ORDER BY total_laid_off DESC) AS Ranking FROM Company_Year WHERE years IS NOT NULL
	)
	SELECT * FROM Company_Year_Rank WHERE Ranking <= 5;

** Key Insights**
•	The maximum number of layoffs in a single event was 12000 
•	The industries most affected by layoffs were Consumer, Retail, Other and Transportation
•	The top country affected by layoffs was the United State, India, and the Netherlands
•	Layoff trends peaked in 2023 and 2022 by Google and Meta, indicating economic downturns

Conclusion
This project provides deep insights into the layoff trends across industries and companies. Understanding these patterns can help businesses and employees prepare for potential market downturns and make informed decisions about job stability and industry growth.








