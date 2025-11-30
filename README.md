# Layoffs-Data-Cleaning-Project
Cleaned and transformed a raw layoffs dataset to produce an analysis-ready table showing standardized company names, consistent locations, calculated layoff percentages, and clean date fields. The process removed duplicates, handled nulls/blanks, normalized text and date formats, and prepared staging tables for downstream analytics and dashboards.
-- DATA  CLEANING  PROJECT 

-- REMOVE DUPLICATES 
-- STANDARDIZATION OF DATA
-- HANDLING NULL VALUES AND BLANKS
-- REMOVING COLUMNS AND ROWS


SELECT *
FROM layoffs;


CREATE TABLE layoffs_stagging
LIKE layoffs;

SELECT * 
FROM layoffs_stagging;

INSERT layoffs_stagging 
SELECT * 
FROM layoffs;



-- HANDING  DUPLICATES 

With duplicate_cte AS (
SELECT * ,
ROW_NUMBER () OVER (PARTITION BY company,location ,industry,total_laid_off,percentage_laid_off,
`date`,stage,country,funds_raised_millions) AS row_num
FROM layoffs_stagging)
SELECT *
FROM duplicate_cte
where row_num  >1;

 
 CREATE TABLE `layoffs_stagging2` (
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


SELECT *
FROM layoffs_stagging2;


INSERT INTO layoffs_stagging2
SELECT * ,
ROW_NUMBER () OVER (PARTITION BY company,location ,industry,total_laid_off,percentage_laid_off,
`date`,stage,country,funds_raised_millions) AS row_num
FROM layoffs_stagging;



DELETE
FROM layoffs_stagging2
WHERE row_num>1;



SELECT *
FROM layoffs_stagging2
WHERE row_num>1;

-- STANDARDIZATION

   SELECT company,TRIM(company)
   FROM layoffs_stagging2; 
   
   
   UPDATE layoffs_stagging2
   SET company =TRIM(company);
   

 SELECT company,TRIM(company)
   FROM layoffs_stagging2; 
   
   
   SELECT distinct country, TRIM(TRAILING '.' FROM  country)
FROM layoffs_stagging2
ORDER BY 1;


UPDATE layoffs_stagging2
SET country =TRIM(TRAILING '.' FROM  country);

SELECT  DISTINCT country 
   FROM layoffs_stagging2; 



SELECT  `date`,
STR_TO_DATE(`date`,'%m/%d/%Y')
   FROM layoffs_stagging2; 
   
   UPDATE layoffs_stagging2
   SET `date`= STR_TO_DATE(`date`,'%m/%d/%Y');
   
   ALTER TABLE layoffs_stagging2
   MODIFY `date` date;
   
 -- HANDLING NULLS  AND BLANKS
     SELECT *
FROM layoffs_stagging2
WHERE total_laid_off IS NULL
AND percentage_laid_off IS NULL ;



SELECT *
FROM layoffs_stagging2
WHERE industry  IS NULL OR industry ='';

UPDATE layoffs_stagging2
SET industry = NULL
WHERE industry = '';


SELECT *
FROM layoffs_stagging2 tb1
JOIN layoffs_stagging2 tb2 
ON tb1.company=tb2.company 
AND tb1.location=tb2.location
 WHERE tb1.industry IS NULL OR tb1.industry =''
 AND tb2.industry IS NOT NULL;
 
 
 SELECT tb1.industry,tb2.industry
FROM layoffs_stagging2 tb1
JOIN layoffs_stagging2 tb2 
ON tb1.company=tb2.company 
 WHERE tb1.industry IS NULL OR tb1.industry =''
 AND tb2.industry IS NOT NULL;
 
 UPDATE layoffs_stagging2 tb1
JOIN layoffs_stagging2 tb2 
ON tb1.company=tb2.company
SET tb1.industry = tb2.industry 
 WHERE tb1.industry IS NULL 
 AND tb2.industry IS NOT NULL;
 
 SELECT *
FROM layoffs_stagging2
WHERE company  = 'airbnb';

SELECT *
FROM layoffs_stagging2
WHERE company  LIKE 'bally%';


SELECT *
FROM layoffs_stagging2;

-- DELETING ROWS

 SELECT *
FROM layoffs_stagging2
WHERE total_laid_off IS NULL
AND percentage_laid_off IS NULL ;

DELETE 
FROM layoffs_stagging2
WHERE total_laid_off IS NULL
AND percentage_laid_off IS NULL ;


 SELECT *
FROM layoffs_stagging2
WHERE total_laid_off IS NULL
AND percentage_laid_off IS NULL ;

SELECT *
FROM layoffs_stagging2;

-- DROPING COLUMN
ALTER TABLE layoffs_stagging2
DROP COLUMN row_num;


SELECT *
FROM layoffs_stagging2;


-- DATA CLEANING DONE.
