# N+1 Problemi

N+1 problemi, əlaqəli verilənlər bazası sorğularında tez-tez rast gəlinən bir performans tələsidir. Xüsusilə Dapper kimi ORM alətləri ilə işləyərkən, düzgün sorğulama aparılmadıqda verilənlər bazasına lazımsız bir şəkildə çox sayda sorğu göndərilməsinə yol açır. Bu vəziyyət, tətbiqinizin performansına ciddi təsir edə bilər.

---

## 1. N+1 Probleminin Tərifi

N+1 problemi, bir siyahıya aid hər bir element üçün ayrı bir sorğu işə salındıqda ortaya çıxır. Bu, ümumilikdə 1 əsas sorğu və N ədəd alt sorğu işə salınması deməkdir.

❌ **Yanlış İstifadə:** Siyahını dövr içində sorğulamaq.

```csharp
var orders = connection.Query<Order>("SELECT * FROM Orders").ToList();

foreach (var order in orders)
{
    order.Customer = connection.QuerySingle<Customer>(
        "SELECT * FROM Customers WHERE Id = @Id",
        new { Id = order.CustomerId });
}

```

Bu üsul, verilənlər bazasına lazımsız yerə çox sayda sorğu göndərir və performansı aşağı salır.

✅ **Düzgün İstifadə:** `JOIN` istifadə edərək bütün verilənləri tək bir sorğuda gətirin.

```csharp
var query = @"
    SELECT o.*, c.* 
    FROM Orders o
    JOIN Customers c ON o.CustomerId = c.Id";

var orders = connection.Query<Order, Customer, Order>(
    query,
    (order, customer) =>
    {
        order.Customer = customer;
        return order;
    },
    splitOn: "Id"
).ToList();
```

---

## 2. Lazy Loading Tələsi

Lazy loading (gec yükləmə), adətən N+1 problemini yaradan bir üsuldur. Dapper, standart olaraq lazy loading dəstəkləmir, lakin manual tətbiq oluna bilər. Bu proses performans problemlərinə yol aça bilər.

❌ **Yanlış İstifadə:** Hər obyekt üçün ayrı sorğu.

```csharp
var orders = connection.Query<Order>("SELECT * FROM Orders").ToList();

foreach (var order in orders)
{
    order.Customer = GetCustomerById(order.CustomerId);
}

Customer GetCustomerById(int customerId)
{
    return connection.QuerySingle<Customer>(
        "SELECT * FROM Customers WHERE Id = @Id",
        new { Id = customerId });
}
```

✅ **Düzgün İstifadə:** Lazım olan bütün verilənləri birləşdirərək gətirin.

```csharp
var query = @"
    SELECT o.Id, o.OrderDate, c.Id AS CustomerId, c.Name AS CustomerName
    FROM Orders o
    JOIN Customers c ON o.CustomerId = c.Id";

var orders = connection.Query<Order, Customer, Order>(
    query,
    (order, customer) =>
    {
        order.Customer = customer;
        return order;
    },
    splitOn: "CustomerId"
).ToList();
```

---

## 3. Performans və Resurs İstifadəsi

- **JOIN İstifadəsi:** N+1 probleminin qarşısını almaq üçün verilənlər bazası sorğularında `JOIN` istifadə edin.
- **Siyahı Əməliyyatları:** Bütün siyahını tək bir sorğu ilə alın, dövr içində sorğu etməkdən qaçının.
- **Profiling Alətləri:** Sorğularınızın verilənlər bazasına neçə dəfə getdiyini izləmək üçün profiling istifadə edin.
