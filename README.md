# SQL---Adventureworks
Question 1
Retrieve information about the products with colour values except null, red, silver/black, white and list price between £75 and £750. Rename the column StandardCost to Price. Also, sort the results in descending order by list price.
SQL Query
SELECT 
    ProductID,
    Name,
    ProductNumber,
    Color,
    StandardCost AS Price,
    ListPrice
FROM 
    [Production].[Product]
WHERE 
    Color IS NULL OR Color NOT IN ('Red', 'Silver/Black', 'White') 
    AND ListPrice BETWEEN 75 AND 750
ORDER BY 
    ListPrice DESC;

