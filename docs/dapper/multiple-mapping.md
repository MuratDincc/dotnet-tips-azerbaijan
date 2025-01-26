# Çoxlu Eşləşdirmə

Dapper, birdən çox obyekti tək bir sorğudan çəkmək üçün **Multiple Mapping** xüsusiyyəti təqdim edir. Bu, xüsusilə əlaqəli verilənlər bazası sorğularında, birdən çox cədvəldən məlumat çəkmə əməliyyatlarında olduqca faydalıdır. Lakin, yanlış istifadə edildiyində mürəkkəb kod strukturuna və performans itkisinə səbəb ola bilər.

---

## 1. Tək Obyekt Eşləşdirmə

❌ **Yanlış İstifadə:** Cədvəlləri manual birləşdirmə.

```csharp
var query = "SELECT * FROM Orders o JOIN Customers c ON o.CustomerId = c.Id";
var data = connection.Query(query).ToList(); // Birləşdirmə aparılmır
```

✅ **Düzgün İstifadə:** Tək bir sorğudan birdən çox obyekti eşləşdirin.

```csharp
var query = "SELECT o.*, c.* FROM Orders o JOIN Customers c ON o.CustomerId = c.Id";
var result = connection.Query<Order, Customer, Order>(
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

## 2. Birdən Çox Obyekt və Mürəkkəb Birləşdirmə

Dapper, birdən çox obyekti birləşdirmək üçün `splitOn` xüsusiyyətini istifadə edir. Bu, hər obyekt üçün ayırıcı sütunlar müəyyənləşdirməyinizə imkan verir.

✅ **Nümunə:**

```csharp
var query = @"
    SELECT o.Id, o.OrderDate, c.Id, c.Name, p.Id, p.Name 
    FROM Orders o 
    JOIN Customers c ON o.CustomerId = c.Id
    JOIN Products p ON o.ProductId = p.Id";

var result = connection.Query<Order, Customer, Product, Order>(
    query,
    (order, customer, product) =>
    {
        order.Customer = customer;
        order.Product = product;
        return order;
    },
    splitOn: "Id,Id"
).ToList();
```

---

## 3. Performans İpuçları

- **splitOn İstifadəsi:** `splitOn` xüsusiyyətini doğru müəyyənləşdirin; əks halda yanlış birləşdirmə aparıla bilər.
- **Lazımsız Məlumatları Daxil Etməyin:** Yalnız ehtiyac duyulan sütunları seçin.
- **Əlaqə İdarəetməsi:** Obyektlər arası əlaqələri kod tərəfində idarə edin.

---

## 4. Çoxlu Birləşdirmə ilə Problemi Həll Etmək

- **Doğru splitOn Ayarı:** `splitOn` xüsusiyyətində sütun adlarının sırasını yoxlayın.
- **Sütun Adlandırma Toqquşmaları:** Əgər eyni adda sütunlar varsa, alias (ləqəb) istifadə edin.

```sql
SELECT 
    o.Id AS OrderId, 
    c.Id AS CustomerId, 
    p.Id AS ProductId 
FROM Orders o 
JOIN Customers c ON o.CustomerId = c.Id
JOIN Products p ON o.ProductId = p.Id
```
