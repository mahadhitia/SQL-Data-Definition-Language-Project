# SQL-Data-Definition-Language-Project

Hi 👋, welcome friends!<br>
In SQL, Data Definition Language (DDL) is a subset of SQL used to define and manage the structural blueprint of a database. On this page, we will explore a DDL project I created to demonstrate how database structures are designed and implemented.

![](https://github.com/mahadhitia/SQL-Data-Definition-Language-Project/blob/main/Images/Mahadhitia-logo.png)

---
## Table of Contents

1. [Project Overview](#project-overview)
2. [Business Problem](#business-problem)
3. [Database Design](#database-design)
4. [Database Implementation](#database-implementation)
   - [Create Database](#create-database)
   - [Create Tables](#create-tables)
   - [Define Constraints and Relationships](#define-constraints-and-relationships)

---

## Project Overview

**Mahadhitia Commerce** is a fictional e-commerce company.
This project focuses on designing and building a relational database using Microsoft SQL Server to store, organize, and manage data.
The database is developed from ground up, starting from creating database, tables, constraints, and relationships between entities.

---

## Business Problem

**Mahadhitia Commerce** has been operating for two years.
As the company grows, the amount of data it needs to manage is continuously increasing.
Initially, the company used spreadsheets to store and manage its data.
However, as the data volume keeps growing, spreadsheets is no longer effective and efficient.
So, in order to address this problem, the company requires a centralized relational database.

---

## Database Design

**Mahadhitia Commerce** initially manages its operational data across multiple spreadsheet-based datasets.
Before implementing the database in SQL Server, the data entities and their relationships were analyzed and converted into a relational database model.
The following Entity Relationship Diagram (ERD) illustrates the relationships between the tables:

![](https://github.com/mahadhitia/SQL-Data-Definition-Language-Project/blob/main/Images/Database-Design.png)

---

## Database Implementation

### Create Database

``` sql
USE master;
GO

-- Drop and recreate the 'MahadhitiaCommerceDB' database
IF EXISTS (
	SELECT 1
	FROM sys.databases
	WHERE name = 'MahadhitiaCommerceDB'
)
BEGIN
	ALTER DATABASE MahadhitiaCommerceDB
		SET SINGLE_USER WITH ROLLBACK IMMEDIATE;

	DROP DATABASE MahadhitiaCommerceDB;
END;
GO

-- Create the 'MahadhitiaCommerceDB' database
CREATE DATABASE MahadhitiaCommerceDB;
GO

USE MahadhitiaCommerceDB;
GO
```

### Create Tables

``` sql
-- Create the 'Categories' table
CREATE TABLE dbo.Categories (
	CategoryID INT IDENTITY(1,1),
	CategoryName VARCHAR(100) NOT NULL
);
GO

-- Create the 'Suppliers' table
CREATE TABLE dbo.Suppliers (
	SupplierID INT IDENTITY(1,1),
	SupplierName VARCHAR(100) NOT NULL,
	Email VARCHAR(150),
	City VARCHAR(50)
);
GO

-- Create the 'Warehouses' table
CREATE TABLE dbo.Warehouses (
	WarehouseID INT IDENTITY(1,1),
	WarehouseName VARCHAR(100) NOT NULL,
	City VARCHAR(50) NOT NULL,
	Capacity INT NOT NULL,
	IsActive BIT NOT NULL
);
GO

-- Create the 'Customers' table
CREATE TABLE dbo.Customers (
	CustomerID INT IDENTITY(1,1),
	CustomerName VARCHAR(100) NOT NULL,
	Email VARCHAR(150) NOT NULL,
	City VARCHAR(50),
	CreatedDate DATE NOT NULL
);
GO

-- Create the 'Products' table
CREATE TABLE dbo.Products (
	ProductID INT IDENTITY(1,1),
	ProductName VARCHAR(150) NOT NULL,
	Price DECIMAL(10,2) NOT NULL,
	CategoryID INT NOT NULL,
	SupplierID INT NOT NULL,
	CreatedDate DATE NOT NULL,
	IsActive BIT NOT NULL
);
GO

-- Create the 'Orders' table
CREATE TABLE dbo.Orders (
	OrderID INT IDENTITY(1,1),
	CustomerID INT NOT NULL,
	OrderDate DATE NOT NULL,
	OrderStatus VARCHAR(20) NOT NULL,
	TotalAmount DECIMAL(12,2) NOT NULL,
	CancelledDate DATE,
	ShippedDate DATE
);
GO

-- Create the 'OrderItems' table
CREATE TABLE dbo.OrderItems (
	OrderItemID INT IDENTITY(1,1),
	OrderID INT NOT NULL,
	ProductID INT NOT NULL,
	Quantity INT NOT NULL,
	UnitPrice DECIMAL(10,2) NOT NULL,
	DiscountPercent DECIMAL(5,2)
);
GO

-- Create the 'Inventory' table
CREATE TABLE dbo.Inventory (
	InventoryID INT IDENTITY(1,1),
	ProductID INT NOT NULL,
	WarehouseID INT NOT NULL,
	StockQuantity INT NOT NULL,
	LastUpdated DATETIME2(0) NOT NULL
);
GO

-- Create the 'Payments' table
CREATE TABLE dbo.Payments (
	PaymentID INT IDENTITY(1,1),
	OrderID INT NOT NULL,
	PaymentDate DATETIME2(0) NOT NULL,
	Amount DECIMAL(12,2) NOT NULL,
	PaymentMethod VARCHAR(30) NOT NULL,
	PaymentStatus VARCHAR(20) NOT NULL,
	PaymentReference VARCHAR(100)
);
GO

-- Create the 'Shipments' table
CREATE TABLE dbo.Shipments (
	ShipmentID INT IDENTITY(1,1),
	OrderID INT NOT NULL,
	WarehouseID INT NOT NULL,
	ShipmentDate DATETIME2(0),
	ShipmentStatus VARCHAR(20) NOT NULL,
	TrackingNumber VARCHAR (100)
);
GO

-- Create the 'Returns' table
CREATE TABLE dbo.[Returns] (
	ReturnID INT IDENTITY(1,1),
	OrderID INT NOT NULL,
	ProductID INT NOT NULL,
	ReturnDate DATE NOT NULL,
	Quantity INT NOT NULL,
	Reason VARCHAR(255),
	Status VARCHAR(20) NOT NULL
);
GO
```

### Define Constraints and Relationships

---

