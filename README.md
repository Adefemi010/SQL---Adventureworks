# SQL---Adventureworks

## Question 1

Retrieve information about the products with colour values except null, red, silver/black, white and list price between £75 and £750. Rename the column StandardCost to Price. Also, sort the results in descending order by list price.

## SQL Query 1

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

## Question 2

Find all the male employees born between 1962 to 1970 and with hire date greater than 2001 and female employees born between 1972 and 1975 and hire date between 2001 and 2002. 

## SQL Query 2

    SELECT 
    * FROM [HumanResources].[Employee]
    WHERE (Gender = 'M' AND BirthDate BETWEEN '1962-01-01' AND '1970-12-31' AND HireDate > '2001-12-31')
    OR
    (Gender = 'F' AND BirthDate BETWEEN '1972-01-01' AND '1975-12-31' AND HireDate BETWEEN '2001-01-01' AND '2002-12-31')


## Question 3

Create a list of 10 most expensive products that have a product number beginning with ‘BK’. Include only the productID, Name and colour. 

## SQL Query 3

SELECT TOP 10
ProductID, Name, Color
FROM [Production].[Product]
WHERE ProductNumber LIKE 'BK%'
ORDER BY ListPrice DESC


## Question 4

Create a list of all contact persons, where the first 4 characters of the last name are the same as the first four characters of the email address. Also, for all contacts whose first name and the last name begin with the same characters, create a new column called full name combining first name and the last name only. Also provide the length of the new column full name.

## SQL Query 4

SELECT PP.BusinessEntityID, PP.FirstName, PP.LastName, PE.EmailAddress,
    CASE 
        WHEN LEFT( PP.FirstName, 1) = LEFT(PP.LastName, 1) THEN CONCAT(PP.FirstName, ' ', PP.LastName) 
        ELSE NULL 
    END AS FullName,
    LEN(CONCAT(PP.FirstName, ' ', PP.LastName)) AS FullNameLength
FROM [Person].[Person] AS PP
JOIN [Person].[EmailAddress] AS PE
ON  PP.BusinessEntityID = PE.BusinessEntityID
WHERE 
    LEFT(PP.LastName, 4) = LEFT(PE.EmailAddress, 4)

## QUESTION 5 

Return all product subcategories that take an average of 3 days or longer to manufacture.

## SQL Query 5

SELECT DISTINCT PS.Name, PP.DaysToManufacture, PS.ProductSubcategoryID
FROM [Production].[Product] AS PP
INNER JOIN [Production].[ProductSubcategory] AS PS
ON PP.ProductSubcategoryID = PS.ProductSubcategoryID
WHERE PP.DaysToManufacture >= 3


## QUESTION 6

Create a list of product segmentation by defining criteria that places each item in a predefined segment as follows. If price gets less than £200 then low value. If price is between £201 and £750 then mid value. If between £750 and £1250 then mid to high value else higher value. Filter the results only for black, silver and red color products. 

## SQL Query 6

SELECT NAME, Color, ListPrice,
CASE
WHEN ListPrice < 200 THEN 'low value'
WHEN ListPrice BETWEEN 201  AND 750 THEN 'mid value'
WHEN ListPrice BETWEEN 750  AND 1250 THEN 'mid to High value'
ELSE 'higher value'
END AS Product_Segmentation
FROM [Production].[Product]
WHERE Color IN ('black', 'silver', 'red')


## Question 7

How many Distinct Job title is present in the Employee table? UESTION 7

## SQL Query 7

SELECT
COUNT (DISTINCT JobTitle) AS NumberOfJobtitle
FROM [HumanResources].[Employee]

## Question 8

Use employee table and calculate the ages of each employee at the time of hiring.

## SQL Query 8

SELECT BusinessEntityID, JobTitle, 
DATEDIFF(YEAR, BirthDate, HireDate) 
AS AgeAtHiring 
FROM [HumanResources].[Employee]

## Question 9

How many employees will be due a long service award in the next 5 years, if long service is 20 years? 

## SQL Query 9

SELECT 
COUNT (*) AS LongServiceAwardsDue
FROM [HumanResources].[Employee]
WHERE
DATEDIFF(YEAR, HireDate, DATEADD(YEAR, 5, GETDATE())) >= 20

## Question 10

How many more years does each employee have to work before reaching sentiment, if sentiment age is 65? 

## SQL Query 10

SELECT 
BusinessEntityID, JobTitle, 
65 - DATEDIFF(YEAR, BirthDate, GETDATE()) AS YearsToRetirement
FROM [HumanResources].[Employee]

## Question 11

Implement new price policy on the product table base on the colour of the item 
If white increase price by 8%, If yellow reduce price by 7.5%, If black increase price by 17.2%. If multi, silver,
silver/black or blue take the square root of the price and double the value. Column should be called Newprice. For
each item, also calculate commission as 37.5% of newly computed list price

## SQL Query 11

select ListPrice, color,
CASE
    WHEN Color = 'white' THEN ListPrice * 1.08
    WHEN Color = 'yellow' THEN Listprice * 0.925
    WHEN Color = 'black' THEN Listprice * 1.172
    WHEN Color IN ('multi', 'silver', 'silver/black', 'blue') THEN SQRT(Listprice) * 2
    ELSE ListPrice
END as NewPrice,
CASE
WHEN Color = 'white' THEN ListPrice * 1.08 * 0.375
    WHEN Color = 'yellow' THEN Listprice * 0.925 * 0.375
    WHEN Color = 'black' THEN Listprice * 1.172 * 0.375
    WHEN Color IN ('multi', 'silver', 'silver/black', 'blue') THEN SQRT(Listprice) * 2 * 0.375
    ELSE ListPrice * 0.375
END as Commission
from [Production].[Product]



## Question 12

Print the information about all the Sales.Person and their sales quota. For every Sales person you should provide their FirstName, LastName, HireDate, SickLeaveHours and Region where they work. 

## SQL Query 12

SELECT SP.BusinessEntityID, PP.FirstName, PP.LastName, HRE.HireDate, HRE.SickLeaveHours, ST.Name AS Region
FROM [Sales].[SalesPerson]  SP
LEFT JOIN [Person].[Person] AS PP
ON SP.BusinessEntityID = PP.BusinessEntityID
LEFT JOIN [HumanResources].[Employee] AS HRE
ON SP.BusinessEntityID = HRE.BusinessEntityID
LEFT JOIN Sales.SalesTerritory AS ST
ON SP.TerritoryID = ST.TerritoryID;

## Question 13

Using adventure works, write a query to extract the following information. 
• Product name 
• Product category name
• Product subcategory name
• Sales person
• Revenue
• Month of transaction
• Quarter of transaction
• Region

## SQL Query 13

SELECT PP.Name as ProductName,
PC.Name AS CategoryName,
PS.Name AS SubCategoryName,
PPP.FirstName + ' ' + PPP.LastName AS SalesPersonName,       
SOH.TotalDue AS Revenue,
MONTH(SOH.OrderDate) AS MonthofTransaction, 
DATEPART(QUARTER, SOH.OrderDate) AS QuarterOfTransaction,
ST.Name AS Region
FROM [Production].[Product] AS PP
INNER JOIN  [Production].[ProductSubcategory] AS PS
ON PP.ProductSubcategoryID = PS.ProductSubcategoryID
INNER JOIN [Production].[ProductCategory] AS PC 
ON PS.ProductCategoryID = PC.ProductCategoryID
INNER JOIN Sales.SalesOrderDetail AS SOD
ON PP.ProductID = SOD.ProductID
INNER JOIN Sales.SalesOrderHeader AS SOH
ON SOD.SalesOrderID = SOH.SalesOrderID
INNER JOIN Sales.SalesPerson SP
ON SOH.SalesPersonID = SP.BusinessEntityID
INNER JOIN Sales.SalesTerritory AS ST
ON SP.TerritoryID = ST.TerritoryID
INNER JOIN [Person].[Person] AS PPP
ON SP.BusinessEntityID = PPP.BusinessEntityID
INNER JOIN [HumanResources].[Employee] AS HRE
ON SP.BusinessEntityID = HRE.BusinessEntityID;

## QUESTION 14

Display the information about the details of an order i.e. order number, order date, amount of order, which customer gives the order and which salesman works for that customer and how much commission he gets for an order. 

## SQL Query 14

SELECT SO.SalesOrderID AS OrderNumber, SO.OrderDate, SO.TotalDue AS OrderAmount, SC.CustomerID,
PP.FirstName + ' ' + PP.LastName AS SalesPersonName, (SO.TotalDue * SP.CommissionPct) AS Commission
FROM 
Sales.SalesOrderHeader AS SO
LEFT JOIN Sales.Customer AS SC
ON SO.CustomerID = SC.CustomerID
LEFT JOIN [Sales].[SalesPerson] AS SP
ON SP.BusinessEntityID = SO.SalesPersonID 
LEFT JOIN [Person].[Person] AS PP
ON SP.BusinessEntityID = PP.BusinessEntityID
LEFT JOIN [HumanResources].[Employee] AS HRE
ON PP.BusinessEntityID = HRE.BusinessEntityID


## QUESTION 15

For all the products calculate 
- Commission as 14.790% of standard cost,
- Margin, if standard cost is increased or decreased as follows: 
Black: +22%,
Red: -12%
Silver: +15%
Multi: +5%
White: Two times original cost divided by the square root of cost
For other colours, standard cost remains the same 

## SQL Query 15

SELECT 
    ProductID,
    Name,
    Color,
    StandardCost,
    StandardCost * 0.1479 AS Commission,
    CASE
        WHEN Color = 'Black' THEN StandardCost * 1.22
        WHEN Color = 'Red' THEN StandardCost * 0.88
        WHEN Color = 'Silver' THEN StandardCost * 1.15
        WHEN Color = 'Multi' THEN StandardCost * 1.05
        WHEN Color = 'White' THEN (2 * StandardCost) / SQRT(StandardCost)
        ELSE StandardCost
    END AS Margin
FROM 
    Production.Product

## QUESTION 16

Create a view to find out the top 5 most expensive products for each colour.

## SQL Query

CREATE VIEW Top5ExpensiveProductsPerColor AS
SELECT 
    Color, 
    Name, 
    ListPrice
FROM 
(
    SELECT 
        PP.Color, 
        PP.Name, 
        PP.ListPrice, 
        ROW_NUMBER() OVER(PARTITION BY PP.Color ORDER BY PP.ListPrice DESC) as RN
    FROM 
        [Production].[Product] AS PP
) T
WHERE 
    T.RN <= 5;

