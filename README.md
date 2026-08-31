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
   - [Add Constraints](#add-constraints)
   - [Add Relationships](#add-relationships)

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

### Add Constraints

```sql
-- Add constraint to the 'Categories' table
ALTER TABLE dbo.Categories
ADD CONSTRAINT PK_Categories
		PRIMARY KEY (CategoryID),

	CONSTRAINT UQ_Categories_CategoryName
		UNIQUE (CategoryName);
GO

-- Add constraint to the 'Suppliers' table
ALTER TABLE dbo.Suppliers
ADD CONSTRAINT PK_Suppliers
		PRIMARY KEY (SupplierID),

	CONSTRAINT UQ_Suppliers_SupplierName
		UNIQUE (SupplierName);
GO

-- Add constaint to the 'Warehouses' table
ALTER TABLE dbo.Warehouses
ADD CONSTRAINT PK_Warehouses
		PRIMARY KEY (WarehouseID),

	CONSTRAINT UQ_Warehouses_WarehouseName
		UNIQUE (WarehouseName),

	CONSTRAINT CK_Warehouses_Capacity
		CHECK (Capacity > 0),

	CONSTRAINT DF_Warehouses_IsActive
		DEFAULT 1 FOR IsActive;
GO

-- Add constraint to the 'Customers' table
ALTER TABLE dbo.Customers
ADD CONSTRAINT PK_Customers
		PRIMARY KEY (CustomerID),

	CONSTRAINT UQ_Customers_Email
		UNIQUE (Email),

	CONSTRAINT DF_Customers_CreatedDate
		DEFAULT GETDATE() FOR CreatedDate;
GO

-- Add constraint to the 'Products' table
ALTER TABLE dbo.Products
ADD CONSTRAINT PK_Products
		PRIMARY KEY (ProductID),

	CONSTRAINT UQ_Products_ProductName
		UNIQUE (ProductName),

	CONSTRAINT CK_Products_Price
		CHECK (Price >= 0),

	CONSTRAINT DF_Products_CreatedDate
		DEFAULT GETDATE() FOR CreatedDate,

	CONSTRAINT DF_Products_IsActive
		DEFAULT 1 FOR IsActive;
GO

-- Add constraint to the 'Orders' table
ALTER TABLE dbo.Orders
ADD CONSTRAINT PK_Orders
		PRIMARY KEY (OrderID),

	CONSTRAINT DF_Orders_OrderDate
		DEFAULT GETDATE() FOR OrderDate,

	CONSTRAINT DF_Orders_OrderStatus
		DEFAULT 'Pending' FOR OrderStatus,

	CONSTRAINT CK_Orders_TotalAmount
		CHECK (TotalAmount >= 0),

	CONSTRAINT CK_Orders_OrderStatus
		CHECK (
				OrderStatus IN (
				'Pending',
				'Processing',
				'Shipped',
				'Delivered',
				'Cancelled'
			)
		),

	CONSTRAINT CK_Orders_CancelledDate
		CHECK (
			CancelledDate IS NULL
			OR CancelledDate >= OrderDate
		),

	CONSTRAINT CK_Orders_ShippedDate
		CHECK (
			ShippedDate IS NULL
			OR ShippedDate >= OrderDate
		);
GO

-- Add constraint to the 'OrderItems' table
ALTER TABLE dbo.OrderItems
ADD CONSTRAINT PK_OrderItems
		PRIMARY KEY (OrderItemID),

	CONSTRAINT CK_OrderItems_Quantity
		CHECK (Quantity > 0),

	CONSTRAINT CK_OrderItems_UnitPrice
		CHECK (UnitPrice >= 0),

	CONSTRAINT DF_OrderItems_DiscountPercent
		DEFAULT 0 FOR DiscountPercent,

	CONSTRAINT CK_OrderItems_DiscountPercent
		CHECK (DiscountPercent BETWEEN 0 AND 100),

	CONSTRAINT UQ_OrderItems_Order_Product
		UNIQUE (OrderID, ProductID);
GO

-- Add constaint to the 'Inventory' table
ALTER TABLE dbo.Inventory
ADD CONSTRAINT PK_Inventory
		PRIMARY KEY (InventoryID),

	CONSTRAINT CK_Inventory_StockQuantity
		CHECK (StockQuantity >= 0),

	CONSTRAINT DF_Inventory_LastUpdated
		DEFAULT SYSDATETIME() FOR LastUpdated,

	CONSTRAINT UQ_Inventory_Product_Warehouse
		UNIQUE (ProductID, WarehouseID);
GO

-- Add constraint to the 'Payments' table
ALTER TABLE dbo.Payments
ADD CONSTRAINT PK_Payments
		PRIMARY KEY (PaymentID),

	CONSTRAINT DF_Payments_PaymentDate
		DEFAULT SYSDATETIME() FOR PaymentDate,

	CONSTRAINT DF_Payments_PaymentStatus
		DEFAULT 'Pending' FOR PaymentStatus,

	CONSTRAINT CK_Payments_Amount
		CHECK (Amount > 0),

	CONSTRAINT CK_Payments_PaymentMethod
		CHECK (
				PaymentMethod IN (
				'Cash',
				'Credit Card',
				'Bank Transfer',
				'E-Wallet'
			)
		),

	CONSTRAINT CK_Payments_PaymentStatus
		CHECK (
				PaymentStatus IN (
				'Pending',
				'Completed',
				'Failed',
				'Refunded'
			)
		);
GO

-- Add constraint to the 'Shipments' table
ALTER TABLE dbo.Shipments
ADD CONSTRAINT PK_Shipments
		PRIMARY KEY (ShipmentID),

	CONSTRAINT DF_Shipments_ShipmentStatus
		DEFAULT 'Pending' FOR ShipmentStatus,

	CONSTRAINT CK_Shipments_ShipmentStatus
		CHECK (
				ShipmentStatus IN (
				'Pending',
				'Processing',
				'Shipped',
				'Delivered',
				'Cancelled'
			)
		);
GO

-- Add constraint to the 'Returns' table
ALTER TABLE dbo.[Returns]
ADD CONSTRAINT PK_Returns
		PRIMARY KEY (ReturnID),

	CONSTRAINT CK_Returns_Quantity
		CHECK (Quantity > 0),

	CONSTRAINT DF_Returns_ReturnDate
		DEFAULT GETDATE() FOR ReturnDate,

	CONSTRAINT DF_Returns_Status
		DEFAULT 'Requested' FOR Status,

	CONSTRAINT CK_Returns_Status
		CHECK (
				Status IN (
				'Requested',
				'Approved',
				'Rejected',
				'Completed'
			)
		);
GO
```

---

### Add Relationships

---

