# Stored Procedure İstifadəsi

Dapper, stored procedure'lər ilə effektiv və performanslı verilənlər bazası əməliyyatları həyata keçirməyə imkan verir. Stored procedure'lər, xüsusilə mürəkkəb verilənlər bazası əməliyyatları və çoxlu sorğular üçün seçilir. Lakin, düzgün istifadə edilmədikdə performans problemlərinə və idarəetmə çətinliklərinə yol aça bilər.

---

## 1. Stored Procedure Çağırma

❌ **Yanlış İstifadə:** Stored procedure'ü birbaşa mətn olaraq çağırmaq.

```csharp
var query = "EXEC GetUsers";
var users = connection.Query<User>(query);
```

✅ **Düzgün İstifadə:** `CommandType.StoredProcedure` istifadə edərək proseduru çağırın.

```csharp
var users = connection.Query<User>("GetUsers", commandType: CommandType.StoredProcedure);
```

---

## 2. Parametrli Stored Procedure İstifadəsi

❌ **Yanlış İstifadə:** Parametrləri əllə birləşdirmək.

```csharp
var query = $"EXEC GetUsersByAge {age}";
var users = connection.Query<User>(query);
```

✅ **Düzgün İstifadə:** Parametrləri düzgün şəkildə təyin edin.

```csharp
var users = connection.Query<User>(
    "GetUsersByAge",
    new { Age = age },
    commandType: CommandType.StoredProcedure);
```

---

## 3. Output Parametrləri İstifadəsi

Dapper, stored procedure'dən dönən `output` parametrlərini asanlıqla idarə edə bilər.

✅ **Nümunə:**

```csharp
var parameters = new DynamicParameters();
parameters.Add("InputParam", 10);
parameters.Add("OutputParam", dbType: DbType.Int32, direction: ParameterDirection.Output);

connection.Execute("CalculateTotal", parameters, commandType: CommandType.StoredProcedure);

var total = parameters.Get<int>("OutputParam");
Console.WriteLine($"Total: {total}");
```

---

## 4. Çoxlu Nəticə Çoxluğu (Multiple Result Sets)

Dapper, stored procedure'lərdən dönən birdən çox nəticə toplusunu da dəstəkləyir.

✅ **Nümunə:**

```csharp
using var multi = connection.QueryMultiple("GetUsersAndOrders", commandType: CommandType.StoredProcedure);

var users = multi.Read<User>().ToList();
var orders = multi.Read<Order>().ToList();
```

---

## 5. Performans və Təhlükəsizlik Məsləhətləri

- **Parametr İstifadəsi:** SQL injeksiyonunun qarşısını almaq üçün hər zaman parametrli sorğuları seçin.
- **CommandType.StoredProcedure:** Prosedur çağırışlarında bu parametri mütləq əlavə edin.
- **İndeks və Query Planlarına Diqqət:** Stored procedure'lərinizin səmərəli işlədiyindən əmin olun.

```csharp
var parameters = new { StartDate = DateTime.UtcNow.AddDays(-30), EndDate = DateTime.UtcNow };
var results = connection.Query("GetReportData", parameters, commandType: CommandType.StoredProcedure);
```
