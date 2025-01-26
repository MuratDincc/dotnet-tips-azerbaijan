# Əsas CRUD Əməliyyatları

Dapper, sadə və performanslı məlumat əldə etməyi təmin etmək üçün istifadə edilən bir kitabxanadır. CRUD (Create, Read, Update, Delete) əməliyyatları Dapper ilə olduqca asandır, lakin yanlış istifadəsi təhlükəsizlik açıqlarına və performans problemlərinə səbəb ola bilər.

---

## 1. Məlumat Əlavə Etmə (Insert)

❌ **Yanlış İstifadə:** SQL injection açıq sorğular yazmaq.

```csharp
var query = $"INSERT INTO Users (Name, Age) VALUES ('{name}', {age})";
connection.Execute(query);
```

✅ **Düzgün İstifadə:** Parametrləri təhlükəsiz bir şəkildə istifadə edin.

```csharp
var query = "INSERT INTO Users (Name, Age) VALUES (@Name, @Age)";
connection.Execute(query, new { Name = name, Age = age });
```

---

## 2. Məlumat Oxuma (Read)

❌ **Yanlış İstifadə:** Bütün cədvəli yaddaşa yükləmək.

```csharp
var query = "SELECT * FROM Users";
var users = connection.Query<User>(query).ToList();
```

✅ **Düzgün İstifadə:** Filtirləmə və projeksiyon ilə daha az məlumat gətirin.

```csharp
var query = "SELECT Id, Name FROM Users WHERE Age > @Age";
var users = connection.Query<User>(query, new { Age = 25 }).ToList();
```

---

## 3. Məlumat Yeniləmə (Update)

❌ **Yanlış İstifadə:** Bütün sütunları lazımsız yerə yeniləmək.

```csharp
var query = "UPDATE Users SET Name = 'UpdatedName', Age = 30 WHERE Id = @Id";
connection.Execute(query, new { Id = userId });
```

✅ **Düzgün İstifadə:** Yalnız lazım olan sütunları yeniləyin.

```csharp
var query = "UPDATE Users SET Name = @Name WHERE Id = @Id";
connection.Execute(query, new { Name = "UpdatedName", Id = userId });
```

---

## 4. Məlumat Silmə (Delete)

❌ **Yanlış İstifadə:** Şərt olmadan məlumat silmək.

```csharp
var query = "DELETE FROM Users";
connection.Execute(query);
```

✅ **Düzgün İstifadə:** Silmə əməliyyatlarını hər zaman filtirləyin.

```csharp
var query = "DELETE FROM Users WHERE Id = @Id";
connection.Execute(query, new { Id = userId });
```

---

## 5. Performans və Təhlükəsizlik İpuçları

- **Prepared Statements:** Hər zaman parametrlərlə sorğular istifadə edərək SQL enjeksiyonunu önləyin.
- **Minimal Məlumat Gətirmə:** Bütün cədvəli yaddaşa yükləmək yerinə ehtiyacınız olan sütunları seçin.
- **Transaction İstifadəsi:** Birdən çox əməliyyatı birlikdə idarə etmək üçün transaction istifadə etməyi düşünün.

```csharp
using var transaction = connection.BeginTransaction();
try
{
    var insertQuery = "INSERT INTO Users (Name, Age) VALUES (@Name, @Age)";
    connection.Execute(insertQuery, new { Name = "Murat", Age = 33 }, transaction);

    var updateQuery = "UPDATE Users SET Age = @Age WHERE Name = @Name";
    connection.Execute(updateQuery, new { Name = "Murat", Age = 34 }, transaction);

    transaction.Commit();
}
catch
{
    transaction.Rollback();
    throw;
}
```
