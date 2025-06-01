# Data-cleaning-project
Working on personal laid off by MNC dataset
** DATA CLEANING Project**/

/**creating and instering duplicate table to avoid any changes in raw data**/ 

create table layoff_stag3
Like layoffs;

Insert into layoff_stag3
select *
from layoffs;

Select *
from layoff_stag3;
 
/** to see the duplicate rows with help of row_number() I'm creating CTE**/

With Duplicate_CT as
(
	select *,
    row_number()over(partition by company,location,industry,total_laid_off,percentage_laid_off
    ,`date`,stage,country,funds_raised_millions) as row_num
    from layoff_stag3
)
select *    from Duplicate_CT
where row_num>1;
    
/** creating another table with row number to delete the duplicates**/

CREATE TABLE `layoff_dup` (
  `company` text,
  `location` text,
  `industry` text,
  `total_laid_off` int DEFAULT NULL,
  `percentage_laid_off` text,
  `date` text,
  `stage` text,
  `country` text,
  `funds_raised_millions` int DEFAULT NULL,
  `row_num` int
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;


insert into layoff_dup
select *, row_number() over(partition by company,location,industry,total_laid_off,percentage_laid_off
,`date`,stage,country,funds_raised_millions) as row_num
from layoff_stag3;
select *
from layoff_dup
where row_num>1
;

delete 
from layoff_dup
where row_num>1;

select* from layoff_dup;

/** Stabilizing the data**/

select company,trim(company)
from layoff_dup; 

update layoff_dup
SET company= trim(company);  

select industry,trim(industry)
from layoff_dup; 
update layoff_dup
SET industry= trim(industry);

select location ,trim(location)
from layoff_dup; 
update layoff_dup
SET location= trim(location);  

Select industry from layoff_dup
Where industry Like 'Crypto%';
 
update layoff_dup
set industry = 'Crypto'
where industry Like 'Crypto%';

select country
from layoff_dup
order by country;

update layoff_dup
set country = trim('.'from country)
where country like 'United States%';

select `date` , 
STR_TO_DATE(`date`,'%m/%d/%Y') as `Date`
from layoff_dup;

Update layoff_dup
set `date` = str_to_date(`date`,'%m/%d/%Y');

Alter table layoff_dup
modify column `date` Date;


select * from layoff_dup
where industry Is Null 
OR industry ='';

select *
from layoff_dup
where total_laid_off IS NULL and Percentage_laid_off IS NULL;
drop table layoff_staging2;
delete 
from layoff_dup
where total_laid_off IS NULL and Percentage_laid_off IS NULL;


select * from layoff_dup
where company = 'Airbnb';

select *
from layoff_dup t1
join layoff_dup t2
	on t1.company = t2.company
    and t1.location = t2.location
where (t1.industry Is Null or t1.industry ='') And t2.industry Is not NUll;

update layoff_dup
set industry = NULL
where industry ='';

update layoff_dup t1
join layoff_dup t2
	on t1.company = t2.company
    and t1.location = t2.location
set t1.industry = t2.industry
where (t1.industry Is Null ) And t2.industry Is not NUll;

Alter table layoff_dup
drop column row_num;
 
