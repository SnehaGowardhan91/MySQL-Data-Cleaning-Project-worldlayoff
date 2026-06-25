# MySQL-Data-Cleaning-Project-worldlayoff
This project focuses on cleaning and preparing a raw housing dataset using MySQL to make it ready for analysis. The project was completed as part of the data analyst portfolio track guided by Alex the Analyst on YouTube.
MySQL Data Cleaning Project: World Layoffs Dataset

#Project Overview

This project focuses on cleaning and preparing a raw dataset containing global tech layoffs data from 2020 onwards. The original dataset was highly unorganized, containing inconsistent data formats, missing values, duplicate entries, and incorrect data types.

The primary objective was to transform this raw data into a structured, clean, and normalized format ready for exploratory data analysis (EDA). This project was completed as part of the data analyst portfolio track guided by Alex the Analyst on YouTube.

* * *

### 🛠️ Tech Stack & SQL Skills Applied

*   **Database Management System:** MySQL Server
*   **SQL Skills Demonstrated:**
    *   Creating staging tables to preserve raw data integrity.
    *   Writing Common Table Expressions (CTEs) and window functions (`ROW_NUMBER()`) to detect duplicates.
    *   Advanced text manipulations (`TRIM`, `TRAILING`).
    *   Date format standardization (`STR_TO_DATE`) and schema modification (`ALTER TABLE`, `MODIFY COLUMN`).
    *   Handling blanks, structural variations, and `NULL` values.

* * *

### 🧼 Cleaning Process Breakdown

The data cleaning pipeline followed a structured 4-step workflow:

### 1\. Remove Duplicates

To preserve the original data, a staging table (`layoffs_staging`) was created. Because the dataset lacked a unique primary key, a `ROW_NUMBER()` function was partitioned over all major columns to find rows appearing more than once.

*   *MySQL Implementation Challenge:* Unlike SQL Server, MySQL does not allow direct deletion from a CTE. To circumvent this, a second staging table (`layoffs_staging`) was created with an explicit `row_num` column, allowing for a clean deletion of any row where `row_num > 1`.

### 2\. Standardize the Data

*   **Text Trimming:** Cleaned up accidental white spaces across text fields using `TRIM()`.
*   **Consolidating Industries:** Fixed data entry variations where the same industry was written differently (e.g., merging variations of 'Crypto', 'Crypto Currency', and 'CryptoCurrency' into one uniform label).
*   **Fixing Trailing Characters:** Removed trailing punctuation from country names (e.g., converting `"United States."` to `"United States"`).
*   **Date Conversion:** Converting the text-based `date` column into a proper SQL Date format.

sql

    -- Temporarily bypass Safe Update Mode for bulk transformation
    SET SQL_SAFE_UPDATES = 0;
    
    -- Standardize string format using uppercase %Y for 4-digit years
    UPDATE layoffs_staging2 
    SET `date` = STR_TO_DATE(`date`, '%m/%d/%Y');
    
    -- Lock in schema change by changing column data type permanently
    ALTER TABLE layoffs_staging2 
    MODIFY COLUMN `date` DATE;
    
    -- Re-enable Safe Update Mode protection
    SET SQL_SAFE_UPDATES = 1;
    



### 3\. Handle Null and Blank Values

*   Converted empty strings (`''`) in the `industry` column into explicit `NULL` values.
*   Used a self-join to look up and populate missing `industry` data where the same `company` and `location` were populated in another row.

### 4\. Remove Unnecessary Columns & Rows

*   Removed rows where both `total_laid_off` and `percentage_laid_off` were completely blank, as these rows provided no actionable analytical insights.
*   Dropped the technical helper column (`row_num`) used during the duplicate removal step to finalize a clean, production-ready table layout.

* * *

### 📈 Key Portfolio Highlights (What I Learned)

*   **MySQL Constraints Handling:** Gained hands-on experience navigating MySQL-specific restrictions, such as disabling `SQL_SAFE_UPDATES` for bulk formatting operations and troubleshooting default SQL Strict Mode behavior.
*   **Date Parsing Quirks:** Successfully debugged MySQL `Error 1292 (Truncated incorrect value)` by diagnosing year-formatting rules, learning that uppercase `%Y` represents 4-digit years while lowercase `%y` represents 2-digit years.
*   **Data Integrity Architecture:** Practiced the standard engineering convention of never performing destructive edits directly on raw database imports.

* * *

### 📂 Repository Structure

*   `layoffs_data_cleaning.sql` — Full, well-commented script containing the end-to-end MySQL cleaning queries.
*   `layoffs.csv` — The raw, uncleaned dataset used as the project's source data.
*   `README.md` — Project documentation and overview.

* * *

### 🤝 Acknowledgments

Special thanks to **Alex Freberg (Alex the Analyst)** for the phenomenal video walkthrough, data source provision, and structural guidance throughout this portfolio track!
