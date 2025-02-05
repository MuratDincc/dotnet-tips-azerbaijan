# Raw SQL Sorğuları

Entity Framework Core, verilənlər bazası ilə əlaqə qurmaq üçün LINQ əsaslı sorğular təklif edir. Lakin, bəzi hallarda Raw SQL sorğularını istifadə etmək lazım ola bilər. Raw SQL sorğuları, yüksək performans və elastiklik təmin etsə də, yanlış istifadələri təhlükəsizlik boşluqlarına və performans problemlərinə yol aça bilər.

---

## 1. SQL Injection Riski

❌ **Yanlış İstifadə:** Dinamik olaraq yaradılan SQL ifadələri.

```csharp
var username = "admin";
var password = "password123";
var query = $"SELECT * FROM Users WHERE Username = '{username}' AND Password = '{password}'";
var users = context.Users.FromSqlRaw(query).ToList();
```

✅ **Düzgün İstifadə:** Parametrli sorğular istifadə edərək SQL Injection riskini önləyin.

```csharp
var username = "admin";
var password = "password123";
var users = context.Users
    .FromSqlInterpolated($"SELECT * FROM Users WHERE Username = {username} AND Password = {password}")
    .ToList();
```

---

## 2. Performansı Göz Ardı Etmək

❌ **Yanlış İstifadə:** Gərəksiz böyük verilən dəstlərini yükləmək.

```csharp
var products = context.Products.FromSqlRaw("SELECT * FROM Products").ToList();
```

✅ **Düzgün İstifadə:** Sorğunu filtrləyərək yalnız lazım olan verilənləri yükləyin.

```csharp
var products = context.Products
    .FromSqlRaw("SELECT * FROM Products WHERE IsActive = 1")
    .ToList();
```

---

## 3. Raw SQL Sorğularını Test Edilə Bilən Hala Gətirməmək

❌ **Yanlış İstifadə:** Raw SQL sorğularını test edilə bilərliyini göz ardı etmək.

```csharp
var orders = context.Orders.FromSqlRaw("SELECT * FROM Orders WHERE OrderDate > GETDATE()").ToList();
```

✅ **Düzgün İstifadə:** SQL sorğularını metodlara daşıyaraq test edilə bilən hala gətirin.

```csharp
public IEnumerable<Order> GetRecentOrders(DateTime sinceDate)
{
    return context.Orders
        .FromSqlInterpolated($"SELECT * FROM Orders WHERE OrderDate > {sinceDate}")
        .ToList();
}
```

---

## 4. Güvənilir Mənbələrdən Gələn SQL İstifadəsi

❌ **Yanlış İstifadə:** SQL ifadələrini birbaşa kod içinə gömmək.

```csharp
var results = context.Database.ExecuteSqlRaw("DELETE FROM Logs WHERE LogDate < '2023-01-01'");
```

✅ **Düzgün İstifadə:** SQL ifadələrini mərkəzi bir yerə daşıyın və ya stored procedure istifadə edin.

```csharp
var results = context.Database.ExecuteSqlRaw("EXEC ClearOldLogs @Date = {0}", new[] { "2023-01-01" });
```

---

## 5. SQL Səhvlərini Göz Ardı Etmək

❌ **Yanlış İstifadə:** Səhv yoxlaması etməmək.

```csharp
var data = context.Users.FromSqlRaw("SELECT * FROM NonExistentTable").ToList();
```

✅ **Düzgün İstifadə:** SQL sorğularını səhv yaxalama mexanizmləri ilə birlikdə istifadə edin.

```csharp
try
{
    var data = context.Users.FromSqlRaw("SELECT * FROM NonExistentTable").ToList();
}
catch (Exception ex)
{
    Console.WriteLine($"SQL Səhvi: {ex.Message}");
}
```

---

## 6. Raw SQL və Entity Framework Xüsusiyyətlərini Birlikdə İstifadə Edə Bilməmək

❌ **Yanlış İstifadə:** Raw SQL sorğuları ilə Entity Framework xüsusiyyətlərini inteqrasiya etməmək.

```csharp
var orders = context.Orders.FromSqlRaw("SELECT * FROM Orders").ToList();
foreach (var order in orders)
{
    context.Entry(order).Collection(o => o.OrderItems).Load();
}
```

✅ **Düzgün İstifadə:** Raw SQL sorğuları ilə Entity Framework əlaqələrini birləşdirin.

```csharp
var orders = context.Orders
    .FromSqlRaw("SELECT * FROM Orders")
    .Include(o => o.OrderItems)
    .ToList();
```
