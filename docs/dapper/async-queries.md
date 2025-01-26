# Async/Await ilə Asinxron Sorğular

Dapper, asinxron sorğular üçün `async/await` dəstəyi təqdim edir. Asinxron proqramlaşdırma, yüksək performanslı və miqyaslana bilən tətbiqlər yaratmağın təməl daşlarından biridir. Lakin, asinxron əməliyyatların yanlış istifadəsi gözlənilməz davranışlara və performans problemlərinə yol aça bilər.

---

## 1. Əsas Asinxron Sorğular

❌ **Yanlış İstifadə:** Asinxron sorğuları sinxron olaraq çağırmaq.

```csharp
var query = "SELECT * FROM Users";
var users = connection.QueryAsync<User>(query).Result; // Deadlock riski
```

✅ **Düzgün İstifadə:** Asinxron çağırışları hər zaman `await` ilə gözləyin.

```csharp
var query = "SELECT * FROM Users";
var users = await connection.QueryAsync<User>(query);
```

---

## 2. Birdən Çox Asinxron Sorğu İdarəetməsi

❌ **Yanlış İstifadə:** Sorğuları ardıcıl işlətmək.

```csharp
var orders = await connection.QueryAsync<Order>("SELECT * FROM Orders");
var customers = await connection.QueryAsync<Customer>("SELECT * FROM Customers");
```

✅ **Düzgün İstifadə:** Sorğuları eyni anda başladın və `Task.WhenAll` ilə gözləyin.

```csharp
var ordersTask = connection.QueryAsync<Order>("SELECT * FROM Orders");
var customersTask = connection.QueryAsync<Customer>("SELECT * FROM Customers");

await Task.WhenAll(ordersTask, customersTask);

var orders = ordersTask.Result;
var customers = customersTask.Result;
```

---

## 3. Transaction ilə Asinxron Əməliyyatlar

Asinxron əməliyyatları transaction ilə birləşdirmək mümkündür.

✅ **Nümunə:**

```csharp
using var transaction = connection.BeginTransaction();

try
{
    var insertQuery = "INSERT INTO Orders (CustomerId) VALUES (@CustomerId)";
    await connection.ExecuteAsync(insertQuery, new { CustomerId = 1 }, transaction);

    var updateQuery = "UPDATE Customers SET IsActive = @IsActive WHERE Id = @Id";
    await connection.ExecuteAsync(updateQuery, new { IsActive = true, Id = 1 }, transaction);

    transaction.Commit();
}
catch
{
    transaction.Rollback();
    throw;
}
```

---

## 4. Performans və Resurs İdarəetməsi

- **Connection Pooling:** Asinxron əməliyyatlarda "connection pool"un səmərəli istifadə olunduğundan əmin olun.
- **Cancellation Token İstifadəsi:** Uzun vaxt tələb edən əməliyyatlar üçün ləğvetmə mexanizmləri istifadə edin.

✅ **Nümunə:**

```csharp
var cts = new CancellationTokenSource(TimeSpan.FromSeconds(10));

var query = "SELECT * FROM Orders WHERE OrderDate > @Date";
var orders = await connection.QueryAsync<Order>(query, new { Date = DateTime.UtcNow.AddDays(-30) }, cancellationToken: cts.Token);
```

---

## 5. Deadlock Problemlərini Önləmək

❌ **Yanlış İstifadə:** `.Result` və ya `.Wait()` istifadə etmək.

```csharp
var query = "SELECT * FROM Products";
var products = connection.QueryAsync<Product>(query).Result; // Deadlock riski
```

✅ **Düzgün İstifadə:** `await` açar sözünü istifadə edin.

```csharp
var query = "SELECT * FROM Products";
var products = await connection.QueryAsync<Product>(query);
```
