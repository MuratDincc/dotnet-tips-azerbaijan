# QueryMultiple

Dapper, bir sorğudan birdən çox nəticə toplusu almağınıza imkan verir. `QueryMultiple` metodu sayəsində, əlaqəli verilənlər bazalarındakı mürəkkəb sorğuları sadələşdirə və birdən çox cədvəli tək bir əməliyyatda çəkə bilərsiniz. Lakin, bu metodun yanlış istifadəsi performans problemlərinə və verilənlərin uyğunsuzluqlarına yol aça bilər.

---

## 1. Əsas QueryMultiple İstifadəsi

❌ **Yanlış İstifadə:** Sorğu nəticələrini manual bölmək.

```csharp
var orders = connection.Query<Order>("SELECT * FROM Orders").ToList();
var customers = connection.Query<Customer>("SELECT * FROM Customers").ToList();
```

✅ **Düzgün İstifadə:** `QueryMultiple` ilə hər iki nəticəni tək bir sorğuda alın.

```csharp
var query = "SELECT * FROM Orders; SELECT * FROM Customers";

using var multi = connection.QueryMultiple(query);
var orders = multi.Read<Order>().ToList();
var customers = multi.Read<Customer>().ToList();
```

---

## 2. Parametrli Sorğularla QueryMultiple İstifadəsi

✅ **Nümunə:** Parametrlərdən istifadə edərək nəticə topulsunu dinamikləşdirin.

```csharp
var query = "SELECT * FROM Orders WHERE CustomerId = @CustomerId; SELECT * FROM Customers WHERE Id = @CustomerId";

using var multi = connection.QueryMultiple(query, new { CustomerId = 1 });
var orders = multi.Read<Order>().ToList();
var customer = multi.Read<Customer>().FirstOrDefault();
```

---

## 3. Çoxlu Nəticə Toplusu ilə Mürəkkəb Veri Birləşdirmə

Dapper, `QueryMultiple` ilə mürəkkəb verilər strukturlarını idarə etməyi asanlaşdırır.

✅ **Nümunə:**

```csharp
var query = @"
    SELECT o.Id, o.OrderDate, c.Id AS CustomerId, c.Name AS CustomerName
    FROM Orders o
    JOIN Customers c ON o.CustomerId = c.Id;
    SELECT * FROM Products;";

using var multi = connection.QueryMultiple(query);
var orders = multi.Read<OrderWithCustomer>().ToList();
var products = multi.Read<Product>().ToList();
```

Burada `OrderWithCustomer` aşağıdakı kimi bir sinif ola bilər:

```csharp
public class OrderWithCustomer
{
    public int Id { get; set; }
    public DateTime OrderDate { get; set; }
    public Customer Customer { get; set; }
}
```

---

## 4. Performans Məsləhətləri

- **Minimum Veri Çəkmə:** Yalnız ehtiyac duyulan sütunları sorğulayın.
- **Düzgün Sıra:** `QueryMultiple` ilə alınan nəticələri düzgün sırada oxuyun. Yanlış sıra xətalara yol aça bilər.
- **Resurs İdarəetmə:** `QueryMultiple` nəticələri tamamlandıqdan sonra `Dispose` çağıraraq resursları sərbəst buraxmağı unutmayın.
