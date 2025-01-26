# Toplu Əməliyyatlar və Performans Artımı

Dapper, bir dəfəyə çox miqdarda məlumat əməliyyatları (toplu əməliyyatlar) üçün təsirli bir vasitədir. Lakin, bu əməliyyatları doğru qurmazsanız performans problemlərinə yol aça bilərsiniz. Toplu əməliyyatları optimallaşdırmaq, tətbiqinizin resurs sərfiyyatını azaldır və əməliyyat müddətlərini qısaldır.

---

## 1. Təkli Əlavə Etmə və Performans Problemi

❌ **Yanlış İstifadə:** Hər əlavə etmə əməliyyatı üçün ayrı bir sorğu işlətmək.

```csharp
foreach (var user in users)
{
    connection.Execute("INSERT INTO Users (Name, Age) VALUES (@Name, @Age)", user);
}
```

Bu üsul, böyük məlumat dəstlərində verilənlər bazasına lazımsız sorğular göndərərək performansı aşağı salır.

✅ **Düzgün İstifadə:** Bütün məlumatı tək bir əməliyyatla əlavə edin.

```csharp
var query = "INSERT INTO Users (Name, Age) VALUES (@Name, @Age)";
connection.Execute(query, users);
```

---

## 2. Toplu Update Əməliyyatları

❌ **Yanlış İstifadə:** Hər bir yeniləmə üçün ayrı bir sorğu.

```csharp
foreach (var user in users)
{
    connection.Execute("UPDATE Users SET Age = @Age WHERE Id = @Id", user);
}
```

✅ **Düzgün İstifadə:** Tək bir sorğuda toplu yeniləmə.

```csharp
var query = @"
    UPDATE Users 
    SET Age = CASE Id 
        WHEN @Id1 THEN @Age1 
        WHEN @Id2 THEN @Age2 
    END
    WHERE Id IN (@Id1, @Id2)";

connection.Execute(query, new 
{
    Id1 = users[0].Id, Age1 = users[0].Age,
    Id2 = users[1].Id, Age2 = users[1].Age
});
```

---

## 3. Performans Üçün Table-Valued Parameters (TVP)

Dapper, birbaşa TVP dəstəyi təqdim etmir, lakin SQL Server-də TVP istifadə edərək performansı artıra bilərsiniz.

✅ **Nümunə:**

```csharp
var dataTable = new DataTable();
dataTable.Columns.Add("Id", typeof(int));
dataTable.Columns.Add("Name", typeof(string));

foreach (var user in users)
{
    dataTable.Rows.Add(user.Id, user.Name);
}

using var connection = new SqlConnection(connectionString);
using var command = connection.CreateCommand();
command.CommandText = "dbo.BulkInsertUsers";
command.CommandType = CommandType.StoredProcedure;

var parameter = command.Parameters.AddWithValue("@Users", dataTable);
parameter.SqlDbType = SqlDbType.Structured;

connection.Open();
command.ExecuteNonQuery();
```

---

## 4. Performans İpuçları

- **Batching:** Əməliyyatları qruplara ayıraraq sorğu sayını azaldın.
- **Transaction İstifadəsi:** Toplu əməliyyatlar üçün `Transaction` istifadə edərək məlumat tutarlılığını təmin edin.
- **Profiling və İzləmə:** SQL Server-də `Query Execution Plan` istifadə edərək sorğularınızı optimallaşdırın.
