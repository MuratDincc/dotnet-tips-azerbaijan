# Transaction İdarəetməsi

Dapper, birdən çox verilənlər bazası əməliyyatını bir yerdə idarə etmək üçün transaction dəstəyi təklif edir. Transaction idarəetməsi, əməliyyatların hamısı uğurlu olmadıqda edilən dəyişikliklərin geri alınmasını təmin edir və verilənlərin tutarlılığını qoruyur. Lakin, yanlış transaction idarəetməsi verilənlərin itkisinə və tutarsızlıqlara səbəb ola bilər.

---

## 1. Transaction Başlatma və İstifadəsi

❌ **Yanlış İstifadə:** Transaction istifadə etmədən birdən çox əməliyyat həyata keçirmək.

```csharp
connection.Execute("INSERT INTO Orders (CustomerId) VALUES (@CustomerId)", new { CustomerId = 1 });
connection.Execute("INSERT INTO OrderDetails (OrderId, ProductId) VALUES (@OrderId, @ProductId)", new { OrderId = 1, ProductId = 1 });
```

✅ **Düzgün İstifadə:** Bütün əməliyyatları bir transaction içində idarə edin.

```csharp
using var transaction = connection.BeginTransaction();

try
{
    connection.Execute("INSERT INTO Orders (CustomerId) VALUES (@CustomerId)", new { CustomerId = 1 }, transaction);
    connection.Execute("INSERT INTO OrderDetails (OrderId, ProductId) VALUES (@OrderId, @ProductId)", new { OrderId = 1, ProductId = 1 }, transaction);
    
    transaction.Commit();
}
catch
{
    transaction.Rollback();
    throw;
}
```

---

## 2. Nested Transaction İstifadəsi

❌ **Yanlış İstifadə:** Tək bir transaction içində birdən çox transaction başlatmaq.

```csharp
var transaction1 = connection.BeginTransaction();
var transaction2 = connection.BeginTransaction(); // Səhv istifadə
```

✅ **Düzgün İstifadə:** Hər bağlantı üçün yalnız bir transaction başladın.

```csharp
using var transaction = connection.BeginTransaction();
connection.Execute("INSERT INTO Orders (CustomerId) VALUES (@CustomerId)", new { CustomerId = 1 }, transaction);
transaction.Commit();
```

---

## 3. Performans və Təhlükəsizlik Məsləhətləri

- **Transaction Commit Nəzarəti:** Yalnız bütün əməliyyatlar uğurlu olduqda `Commit` çağırın.
- **Timeout Tənzimləməsi:** Uzun sürən transaction'lar üçün bir timeout tənzimləyin.
- **Transaction Kapsamını Daraldın:** Transaction içində yalnız lazımi əməliyyatları edin.

```csharp
using var transaction = connection.BeginTransaction(System.Data.IsolationLevel.Serializable);
try
{
    // Əməliyyatlar
    transaction.Commit();
}
catch
{
    transaction.Rollback();
    throw;
}
```

---

## 4. Isolation Levels (İzolyasiya Səviyyələri)

Dapper, fərqli izolyasiya səviyyələri ilə işləyə bilər. Bu səviyyələr, transaction'ın digər transaction'larla necə qarşılıqlı əlaqədə olacağını müəyyən edir.

### İzolyasiya Səviyyələri:

- **Read Uncommitted:** Digər transaction'ların hələ commit edilməmiş verilənlərini oxuya bilər.
- **Read Committed:** Yalnız commit edilmiş verilənlər oxuna bilər.
- **Repeatable Read:** Bir transaction boyunca eyni məlumatı oxuma zəmanəti verir.
- **Serializable:** Ən yüksək izolyasiya səviyyəsidir, verilənlər bazası kilidləmələrini artıra bilər.

```csharp
using var transaction = connection.BeginTransaction(System.Data.IsolationLevel.RepeatableRead);
```
