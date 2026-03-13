Link: https://services.odata.org/V3/Northwind/Northwind.svc/

______________________________________________________________________________________________________________________________________
=================================R CODE===============================================


AIM:- Perform the data classification using classification algorithm using R/Python.

rainfall <- c(799,1174.8,865.1,1334.6,635.4,918.5,685.5,998.6,784.2,985,882.8,1071)
rainfall.timeseries <- ts(rainfall, start = c(2012,1), frequency = 12)
print(rainfall.timeseries)
png(file = "rainfall.png")
plot(rainfall.timeseries,
     main="Monthly Rainfall Data",
     xlab="Year",
     ylab="Rainfall",
     col="blue",
     type="o")

dev.off()

plot(rainfall.timeseries,
     main="Monthly Rainfall Data",
     xlab="Year",
     ylab="Rainfall",
     col="blue",
     type="o")

_______________________________________________________________________________________________________________________________________________________________________

AIM: Perform data clustering using K-means clustering algorithm using R.


newiris <- iris
newiris$Species <- NULL
kc <- kmeans(newiris, 3)
kc
table(iris$Species, kc$cluster)

plot(newiris[c("Sepal.Length","Sepal.Width")],
     col = kc$cluster,
     main = "K-Means Clustering on Iris Dataset",
     xlab = "Sepal Length",
     ylab = "Sepal Width",
     pch = 19)

points(kc$centers[,c("Sepal.Length","Sepal.Width")],
       col = 1:3,
       pch = 8,
       cex = 2)

png("kmeans_cluster.png")
plot(newiris[c("Sepal.Length","Sepal.Width")],
     col = kc$cluster,
     main = "K-Means Clustering on Iris Dataset",
     xlab = "Sepal Length",
     ylab = "Sepal Width",
     pch = 19)
points(kc$centers[,c("Sepal.Length","Sepal.Width")],
       col = 1:3,
       pch = 8,
       cex = 2)
dev.off()

________________________________________________________________________________________________________________________________________________________

AIM : To perform Linear Regression analysis between Height and Weight using R.

x <- c(151, 174, 138, 186, 128, 136, 179, 163, 152, 131)
y <- c(63, 81, 56, 91, 47, 57, 76, 72, 62, 48)

relation <- lm(y ~ x)
print(relation)
summary(relation)
a <- data.frame(x = 170)
result <- predict(relation, a)
print(result)

png(file = "linearregression.png")

plot(x, y,
     col = "blue",
     main = "Height vs Weight Linear Regression",
     xlab = "Height (cm)",
     ylab = "Weight (kg)",
     pch = 16,
     cex = 1.3)

abline(relation, col = "red", lwd = 2)

dev.off()

plot(x, y,
     col = "blue",
     main = "Height vs Weight Linear Regression",
     xlab = "Height (cm)",
     ylab = "Weight (kg)",
     pch = 16,
     cex = 1.3)

abline(relation, col = "red", lwd = 2)

______________________________________________________________________________________________________________________________________

======================================PYTHON CODE======================================================

Write a Python program to read data from a CSV file, perform simple data analysis, and generate basic insights. (Use Pandas is a Python library).                                                                                                             

import pandas as pd

file_path = "practice2.csv"
df = pd.read_csv(file_path)

print("\nFirst 5 rows of the dataset:")
print(df.head())

print("\nDataset Summary:")
print(df.info())

print("\nBasic Statistics:")
print(df.describe())

print("\nMissing Values in Each Column:")
print(df.isnull().sum())

print("\nColumn Names:")
print(df.columns)

if 'Salary' in df.columns:
    print(f"\nAverage Salary: {df['Salary'].mean():.2f}")

if 'Age' in df.columns:
    print(f"\nYoungest Person's Age: {df['Age'].min()}")
    print(f"Oldest Person's Age: {df['Age'].max()}")

_____________________________________________________________________________________________________________________________________________________________
==================================MY SQL CODE======================================

-- Disable safe update mode
SET SQL_SAFE_UPDATES = 0;

CREATE DATABASE IF NOT EXISTS Sales_Staging;
USE Sales_Staging;

CREATE TABLE IF NOT EXISTS Staging_Sales (
    SalesID      INT PRIMARY KEY,
    OrderDate    DATE,
    ProductName  VARCHAR(100),
    Category     VARCHAR(50),
    Region       VARCHAR(50),
    SalesAmount  DECIMAL(10,2),
    Profit       DECIMAL(10,2),
    Quantity     INT,
    LoadDate     DATETIME DEFAULT CURRENT_TIMESTAMP,
    BatchID      INT
);

CREATE TABLE IF NOT EXISTS Final_Sales (
    SalesID      INT PRIMARY KEY,
    OrderDate    DATE,
    ProductName  VARCHAR(100),
    Category     VARCHAR(50),
    Region       VARCHAR(50),
    SalesAmount  DECIMAL(10,2),
    Profit       DECIMAL(10,2),
    Quantity     INT,
    LoadDate     DATETIME DEFAULT CURRENT_TIMESTAMP
);

INSERT INTO Staging_Sales (SalesID, OrderDate, ProductName, Category, Region, SalesAmount, Profit, Quantity, BatchID)
VALUES
    (1, '2024-01-01', 'Laptop',     'Electronics', 'North', 1200.00, 200.00, 3, 101),
    (2, '2024-01-02', 'Smartphone', 'Electronics', 'South',  800.00, 150.00, 2, 101),
    (3, '2024-01-03', 'Tablet',     'Electronics', 'East',   600.00, 100.00, 5, 101);

DELETE FROM Staging_Sales
WHERE SalesID NOT IN (
    SELECT SalesID FROM (
        SELECT MIN(SalesID) AS SalesID
        FROM Staging_Sales
        GROUP BY OrderDate, ProductName, Region
    ) AS deduped
);

-- Fix 2: Update NULL profits (safe update mode disabled above fixes Error 1175)
UPDATE Staging_Sales
SET Profit = 0
WHERE Profit IS NULL;

INSERT INTO Final_Sales (SalesID, OrderDate, ProductName, Category, Region, SalesAmount, Profit, Quantity)
SELECT SalesID, OrderDate, ProductName, Category, Region, SalesAmount, Profit, Quantity
FROM Staging_Sales;

DELETE FROM Staging_Sales WHERE BatchID = 101;

SET SQL_SAFE_UPDATES = 1;
