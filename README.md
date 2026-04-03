# food
-- Create Database
CREATE DATABASE FoodDeliveryDB;

-- =========================
-- Create Tables
-- =========================

CREATE TABLE Customers (
    CustomerID INT PRIMARY KEY IDENTITY(1,1),
    Name VARCHAR(100),
    Email VARCHAR(100),
    Phone VARCHAR(20)
);

CREATE TABLE Restaurants (
    RestaurantID INT PRIMARY KEY IDENTITY(1,1),
    Name VARCHAR(100),
    Location VARCHAR(100)
);

CREATE TABLE MenuItems (
    ItemID INT PRIMARY KEY IDENTITY(1,1),
    RestaurantID INT,
    ItemName VARCHAR(100),
    Price DECIMAL(10,2),
    FOREIGN KEY (RestaurantID) REFERENCES Restaurants(RestaurantID)
);

CREATE TABLE Orders (
    OrderID INT PRIMARY KEY IDENTITY(1,1),
    CustomerID INT,
    RestaurantID INT,
    OrderDate DATETIME DEFAULT GETDATE(),
    FOREIGN KEY (CustomerID) REFERENCES Customers(CustomerID),
    FOREIGN KEY (RestaurantID) REFERENCES Restaurants(RestaurantID)
);

CREATE TABLE OrderItems (
    OrderItemID INT PRIMARY KEY IDENTITY(1,1),
    OrderID INT,
    ItemID INT,
    Quantity INT,
    FOREIGN KEY (OrderID) REFERENCES Orders(OrderID),
    FOREIGN KEY (ItemID) REFERENCES MenuItems(ItemID)
);

-- =========================
-- Insert Sample Data
-- =========================

INSERT INTO Customers (Name, Email, Phone)
VALUES 
('Alice', 'alice@email.com', '123456789'),
('Bob', 'bob@email.com', '987654321');

INSERT INTO Restaurants (Name, Location)
VALUES 
('Pizza Place', 'New York'),
('Burger House', 'Chicago');

INSERT INTO MenuItems (RestaurantID, ItemName, Price)
VALUES
(1, 'Pepperoni Pizza', 12.50),
(1, 'Cheese Pizza', 10.00),
(2, 'Beef Burger', 8.50),
(2, 'Fries', 3.00);

INSERT INTO Orders (CustomerID, RestaurantID)
VALUES
(1, 1),
(2, 2);

INSERT INTO OrderItems (OrderID, ItemID, Quantity)
VALUES
(1, 1, 2),
(1, 2, 1),
(2, 3, 1),
(2, 4, 2);

-- =========================
-- Queries
-- =========================

-- 1. Orders with customer names
SELECT 
    o.OrderID,
    c.Name AS CustomerName,
    r.Name AS RestaurantName,
    o.OrderDate
FROM Orders o
JOIN Customers c ON o.CustomerID = c.CustomerID
JOIN Restaurants r ON o.RestaurantID = r.RestaurantID;

-- 2. Items in each order
SELECT 
    o.OrderID,
    m.ItemName,
    oi.Quantity,
    m.Price,
    (oi.Quantity * m.Price) AS TotalPrice
FROM OrderItems oi
JOIN Orders o ON oi.OrderID = o.OrderID
JOIN MenuItems m ON oi.ItemID = m.ItemID
ORDER BY o.OrderID;

-- 3. Total price per order
SELECT 
    o.OrderID,
    SUM(oi.Quantity * m.Price) AS OrderTotal
FROM OrderItems oi
JOIN Orders o ON oi.OrderID = o.OrderID
JOIN MenuItems m ON oi.ItemID = m.ItemID
GROUP BY o.OrderID;

# dbdiagram
// =========================
// Tables
// =========================

Table Customers {
  CustomerID integer [primary key, increment]
  Name varchar
  Email varchar
  Phone varchar
}

Table Restaurants {
  RestaurantID integer [primary key, increment]
  Name varchar
  Location varchar
}

Table MenuItems {
  ItemID integer [primary key, increment]
  RestaurantID integer
  ItemName varchar
  Price decimal
}

Table Orders {
  OrderID integer [primary key, increment]
  CustomerID integer
  RestaurantID integer
  OrderDate datetime [default: `GETDATE()`]
}

Table OrderItems {
  OrderItemID integer [primary key, increment]
  OrderID integer
  ItemID integer
  Quantity integer
}

// =========================
// Relationships
// =========================

Ref: MenuItems.RestaurantID > Restaurants.RestaurantID

Ref: Orders.CustomerID > Customers.CustomerID
Ref: Orders.RestaurantID > Restaurants.RestaurantID

Ref: OrderItems.OrderID > Orders.OrderID
Ref: OrderItems.ItemID > MenuItems.ItemID