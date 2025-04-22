# Supply Chain Optimization using SQL

## Project Overview

This project demonstrates how SQL can be used to analyze supply chain data and identify areas for potential optimization. By querying a sample database, we explore key aspects such as bottleneck identification, inventory management, and order fulfillment processes.

The goal is to provide a foundation for understanding how data analysis with SQL can offer valuable insights into supply chain efficiency and potential improvements. This project serves as a practical example for individuals looking to apply their SQL skills to real-world business problems.

The project utilizes a simplified data model representing core supply chain entities like suppliers, products, inventory, customers, orders, and shipments. SQL queries are then developed to extract meaningful information from this data.

## Database Schema

The database consists of the following tables:

1. **Suppliers** - Information about product vendors
2. **Products** - Product details and associated suppliers
3. **Locations** - Warehouses and distribution centers
4. **Inventory** - Current stock levels across locations
5. **Customers** - Customer information
6. **Orders** - Order header information
7. **OrderItems** - Line items within each order
8. **Shipments** - Delivery details for fulfilled orders

### Entity Relationship Diagram

```
Suppliers 1──┐
             └──* Products
             
             Products *──┐
                         ├──* Inventory
Locations 1──┘           │
                         │
                         └──* OrderItems
                         
Customers 1──┐
             └──* Orders 1──┐
                            ├──* OrderItems
                            │
                            └──1 Shipments
```

## Key Analysis Areas

### 1. Supply Chain Bottleneck Identification

Identifying bottlenecks in the supply chain is crucial for improving overall efficiency. The following query helps identify suppliers with potentially longer lead times:

```sql
-- Identify Suppliers with Potentially Longer Lead Times
SELECT
    s.SupplierName,
    AVG((ship.ShipmentDate::date - o.OrderDate::date)) AS AverageLeadTimeDays
FROM
    data.suppliers s
JOIN
    data.products p ON s.SupplierID = p.SupplierID
JOIN
    data.orderItems oi ON p.ProductID = oi.ProductID
JOIN
    data.orders o ON oi.OrderID = o.OrderID
JOIN
    data.shipments ship ON o.OrderID = ship.OrderID
GROUP BY
    s.SupplierName
ORDER BY
    AverageLeadTimeDays DESC;
```

We also identify orders with the longest fulfillment times to pinpoint potential workflow issues:

```sql
-- Identify Orders with Longest Fulfillment Times
SELECT
    o.OrderID,
    o.OrderDate,
    ship.ShipmentDate,
    (ship.ShipmentDate::date - o.OrderDate::date) AS FulfillmentTimeDays
FROM
    data.orders o
JOIN
    data.shipments ship ON o.OrderID = ship.OrderID
WHERE
    ship.ShipmentDate IS NOT NULL AND o.OrderDate IS NOT NULL
ORDER BY
    FulfillmentTimeDays DESC
LIMIT 5;
```

### 2. Inventory Management Optimization

Proper inventory management ensures products are available when needed while minimizing holding costs. This query identifies products that need reordering:

```sql
-- Identify Products Below Reorder Level
SELECT
    p.ProductName,
    i.QuantityOnHand,
    i.ReorderLevel,
    l.LocationName
FROM
    data.inventory i
JOIN
    data.products p ON i.ProductID = p.ProductID
LEFT JOIN
    data.locations l ON i.LocationID = l.LocationID
WHERE
    i.QuantityOnHand < i.ReorderLevel;
```

### 3. Order Fulfillment Analysis

Understanding the efficiency of order processing helps identify opportunities for improvement. The following query calculates the average order fulfillment time:

```sql
-- Calculate Average Order Fulfillment Time
SELECT
    AVG(ship.ShipmentDate::date - o.OrderDate::date) AS AverageFulfillmentTimeDays
FROM
    data.orders o
JOIN
    data.shipments ship ON o.OrderID = ship.OrderID
WHERE
    ship.ShipmentDate IS NOT NULL AND o.OrderDate IS NOT NULL;
```

## Setup Instructions

### Prerequisites
- PostgreSQL database server
- Basic knowledge of SQL

### Installation Steps

1. Create a database in PostgreSQL
2. Execute the table creation scripts found in `database_setup/create_tables.sql`
3. Load sample data using the scripts in `data/sample_data.sql`
4. Run the analysis queries from the `sql_queries` directory

## Sample Data

The project includes sample data to demonstrate the functionality. This data represents:
- 3 suppliers
- 5 products
- 2 warehouse locations
- 2 customers
- 4 orders with associated order items
- 2 shipments

## Future Enhancements

Potential areas for expansion of this project include:

1. **Advanced Analytics** - Implement more sophisticated queries for demand forecasting and trend analysis
2. **Performance Metrics** - Create dashboards with KPIs such as order cycle time, fill rate, and inventory turnover
3. **Integration** - Connect with visualization tools like Tableau or Power BI
4. **Optimization Models** - Develop SQL procedures for route optimization or inventory level calculations





