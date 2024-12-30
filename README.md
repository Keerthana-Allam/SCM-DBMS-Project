# SCM-DBMS-Project
# DBMS Implementation - Supply Chain Management System 📊

## Table of Contents
- [Overview](#overview)
- [Database Design](#database-design)
  - [ER Model](#er-model)
  - [Normalization](#normalization)
  - [Data Cleaning](#data-cleaning)
- [SQL Implementation](#sql-implementation)
  - [Core Tables](#core-tables)
  - [Key Queries](#key-queries)
- [Business Rules](#business-rules)
- [Project Structure](#project-structure)
- [Tech Stack](#tech-stack)
- [Team](#team)

## Overview
An Enterprise Resource Planning (ERP) Database Schema for manufacturing and supply chain operations. Demonstrates comprehensive database design, normalization, and SQL implementation using Oracle APEX.

## Database Design

### ER Model

**Implemented Relationships:**
* One-to-Many (1:M)
  * Product-Raw Material
  * Vendor-Supply Schedule
* Many-to-One (M:1)
  * Raw Material-Production Line
  * Invoice-Department
* Ternary Relationship
  * Vendor-Raw Material-Warehouse (via Supply_Schedule)

**Entity Types:**
* Regular Entities
  * Product
  * Department
  * Employee
  * Vendor
  * Warehouse
  * Production Line
  * Invoice

* Associative Entities
  * Supply_Schedule
  * Department_Employee
  * Warehouse_Employee
  * Line_Employee

### Normalization

#### 1NF Implementation
* Eliminated multivalued attributes
  * Removed "Raw Material" from Product table
  * Created separate RawMaterial relation
* Decomposed composite attributes
  * Address → Street, City
  * Location → Constituent parts

#### 2NF Implementation
* Removed partial dependencies
  * Created RAWMATERIAL_PRODUCT relation
  * Established PRODUCT_PL relation
* Established primary keys for all relations

#### 3NF Implementation
* Eliminated transitive dependencies
  * Created VENDOR_PAYMENTMODE relation
  * Separated payment type from Invoice table

### Data Cleaning
* Fixed product code typo: "PER030" → "PE030"
* Standardized address formats
* Normalized phone numbers
* Validated FK relationships

## SQL Implementation

### Core Tables

```sql
-- Department Table
CREATE TABLE Department (
    DepartmentID VARCHAR2(10) PRIMARY KEY,
    DepartmentName VARCHAR2(30),
    NumberOfEmployees NUMBER(5),
    PhoneNumber VARCHAR2(20),
    Street VARCHAR2(30),
    City VARCHAR2(10)
);

-- Employee Table
CREATE TABLE Employee (
    EmployeeID VARCHAR2(5) PRIMARY KEY,
    FirstName VARCHAR2(15),
    LastName VARCHAR2(15),
    Position VARCHAR2(50),
    Salary VARCHAR2(10),
    WorkCategory VARCHAR2(30)
);

-- Product Table
CREATE TABLE Product (
    ProductNumber VARCHAR2(10) PRIMARY KEY,
    ProductType VARCHAR2(20),
    ProductName VARCHAR2(50),
    EmployeeID VARCHAR2(10),
    Price NUMBER(10),
    Cost NUMBER(10),
    Color VARCHAR2(10),
    Weight NUMBER(10),
    FOREIGN KEY (EmployeeID) REFERENCES Employee(EmployeeID)
);
```

### Key Queries

#### 1. Vendor Management Analysis
```sql
SELECT 
    v.VendorName,
    COUNT(DISTINCT rm.ProductNumber) as Products_Count,
    SUM(i.TotalAmount) as Total_Bills
FROM 
    Vendor v
    LEFT JOIN Supply_Schedule ss ON v.VendorNumber = ss.VendorNumber
    LEFT JOIN RawMaterial_Product rm ON ss.ProductNumber = rm.ProductNumber
    LEFT JOIN Invoice i ON v.VendorNumber = i.VendorNumber
GROUP BY 
    v.VendorName
ORDER BY 
    Total_Bills DESC;
```

#### 2. Production Volume Analysis
```sql
SELECT 
    p.ProductType,
    COUNT(*) as Production_Volume,
    AVG(p.Price) as Avg_Price
FROM 
    Product p
    JOIN Production_Line pl ON p.ProductNumber = pl.ProductNumber
GROUP BY 
    p.ProductType
ORDER BY 
    Production_Volume DESC;
```

## Business Rules
* Departments, warehouses, and production lines have multiple employees
* Employees work in exactly one entity
* Products must be produced by one specific production line
* Raw materials can be supplied through warehouses or directly from vendors
* Vendors submit invoices for orders
* Invoices must be processed by accounting department

## Project Structure
```
project/
├── ddl/
│   ├── tables/
│   ├── constraints/
│   └── indexes/
├── dml/
│   ├── initial_data/
│   └── test_data/
├── queries/
│   ├── vendor_analysis.sql
│   ├── production_volume.sql
│   └── supplier_analysis.sql
└── documentation/
    ├── eer_diagram.pdf
    ├── normalization.md
    └── data_dictionary.xlsx
```

## Tech Stack
* Oracle Database 19c
* Oracle APEX
* SQL Developer

## Team
* **SQL Developer**: Keerthana Allam
* **Business Rules Analyst**: Satwik CR Jinna
* **Database Designer**: Jhansi Sai Vegesna
* **QA Specialist**: Sri Pavani Madani

---
*Fall 2023 Semester Project - MGS 613 DBMS Course*
