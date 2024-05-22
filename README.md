# ironhack-lab-5

## Tasks:

### 1. SQL Query Optimization
Participants are given SQL queries and asked to improve them based on theoretical knowledge of database optimization.

**Provided SQL Queries for Optimization:**

**Orders Query:** Retrieve orders with many items and calculate the total price.
```sql
SELECT Orders.OrderID, SUM(OrderDetails.Quantity * OrderDetails.UnitPrice) AS TotalPrice
FROM Orders
JOIN OrderDetails ON Orders.OrderID = OrderDetails.OrderID
WHERE OrderDetails.Quantity > 10
GROUP BY Orders.OrderID;
```

**Customer Query:** Find all customers from London and sort by CustomerName.
```sql
SELECT CustomerName FROM Customers WHERE City = 'London' ORDER BY CustomerName;
```

#### Optimización de Consultas SQL:

**1. Orders Query Optimizada:**

*Ineficiencias Identificadas:*
- Falta de índices en las columnas usadas en el `JOIN` y en el `WHERE`.
- Posible sobrecarga en el cálculo del `SUM`.
- Selección de todas las columnas en la `SELECT`.

*Optimización Propuesta:*
- Crear índices en las columnas `OrderID` en ambas tablas y en `Quantity` de `OrderDetails`.
- Utilizar un índice compuesto en `OrderDetails` (`OrderID`, `Quantity`).
- Seleccionar solo las columnas necesarias.

```sql
CREATE INDEX idx_orders_orderid ON Orders(OrderID);
CREATE INDEX idx_orderdetails_orderid_quantity ON OrderDetails(OrderID, Quantity);

SELECT Orders.OrderID, SUM(OrderDetails.Quantity * OrderDetails.UnitPrice) AS TotalPrice
FROM Orders
JOIN OrderDetails ON Orders.OrderID = OrderDetails.OrderID
WHERE OrderDetails.Quantity > 10
GROUP BY Orders.OrderID;
```


**2. Customer Query Optimizada:**

*Ineficiencias Identificadas:*
- Falta de índice en la columna `City`.

*Optimización Propuesta:*
- Crear un índice en la columna `City`.

```sql
CREATE INDEX idx_customers_city ON Customers(City);

SELECT CustomerName FROM Customers WHERE City = 'London' ORDER BY CustomerName;
```


### 2. NoSQL Query Implementation
Participants will optimize provided NoSQL queries theoretically, focusing on efficient data retrieval and minimized latency.

**NoSQL Queries for Review:**

**User Posts Query:** Retrieve the most popular active posts and display their title and like count.
```javascript
db.posts
  .find({ status: "active" }, { title: 1, likes: 1 })
  .sort({ likes: -1 });
```

**User Data Aggregation:** Summarize the number of active users by location.
```javascript
db.users.aggregate([
  { $match: { status: "active" } },
  { $group: { _id: "$location", totalUsers: { $sum: 1 } } },
]);
```

#### Optimización de Consultas NoSQL:

**1. User Posts Query Optimizada:**

*Ineficiencias Identificadas:*
- Falta de índice en los campos `status` y `likes`.

*Optimización Propuesta:*
- Crear un índice compuesto en `status` y `likes`.
- Asegurarse de que solo se seleccionan los campos necesarios.

```javascript
db.posts.createIndex({ status: 1, likes: -1 });

db.posts
  .find({ status: "active" }, { title: 1, likes: 1 })
  .sort({ likes: -1 });
```

**2. User Data Aggregation Optimizada:**

*Ineficiencias Identificadas:*
- Falta de índice en el campo `status`.

*Optimización Propuesta:*
- Crear un índice en el campo `status`.
- Optimizar el pipeline de agregación.

```javascript
db.users.createIndex({ status: 1 });

db.users.aggregate([
  { $match: { status: "active" } },
  { $group: { _id: "$location", totalUsers: { $sum: 1 } } },
]);
```
