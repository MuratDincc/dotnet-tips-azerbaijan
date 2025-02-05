# Bölünmüş Sorğu (Split Query)

Entity Framework Core-da Bölünmüş Sorğu, mürəkkəb sorğuları birdən çox SQL sorğusuna bölərək performans yaxşılaşdırması edir. Bu xüsusiyyət, xüsusilə böyük verilən toplularinda verilənlər bazası əməliyyatlarını optimallaşdırmaq üçün istifadə olunur. Lakin, yanlış istifadəsi həm performans, həm də verilənlər uyğunluğu problemlərinə yol aça bilər.

---

## 1. Bölünmüş Sorğunu Gərəksiz İstifadə Etmək

❌ **Yanlış İstifadə:** Bölünmüş Sorğunu kiçik verilən dəstlərində istifadə etmək.

```csharp
var users = context.Users
    .Include(u => u.Orders)
    .AsSplitQuery()
    .ToList();
```

✅ **Düzgün İstifadə:** Bölünmüş Sorğunu yalnız böyük verilən toplusu üçün istifadə edin.

```csharp
var users = context.Users
    .Include(u => u.Orders)
    .AsSplitQuery()
    .Where(u => u.IsActive)
    .ToList();
```

---

## 2. Performans Üstünlüklərini Göz Ardı Etmək

❌ **Yanlış İstifadə:** Bölünmüş Sorğu istifadə etmədən böyük verilən dəstlərini tək bir sorğuda yükləmək.

```csharp
var orders = context.Orders
    .Include(o => o.Customer)
    .Include(o => o.Products)
    .ToList();
```

✅ **Düzgün İstifadə:** Böyük verilən dəstlərini Bölünmüş Sorğu ilə bölərək işləyin.

```csharp
var orders = context.Orders
    .Include(o => o.Customer)
    .Include(o => o.Products)
    .AsSplitQuery()
    .ToList();
```

---

## 3. Bölünmüş Sorğu və İzləmə Dəyişikliklərini Yanlış İdarə Etmək

❌ **Yanlış İstifadə:** Bölünmüş Sorğu istifadə edərkən verilənlər bazası dəyişikliklərini yanlış anlamaq.

```csharp
var customers = context.Customers
    .Include(c => c.Orders)
    .AsSplitQuery()
    .ToList();

customers[0].Name = "Güncəllənmiş Ad";
context.SaveChanges(); // Gözlənilməyən nəticələr
```

✅ **Düzgün İstifadə:** Bölünmüş Sorğunun yalnız oxumaq əməliyyatları üçün daha uyğun olduğunu unutmayın.

```csharp
var customers = context.Customers
    .Include(c => c.Orders)
    .AsSplitQuery()
    .ToList();

// Dəyişiklik etmək yerinə yeni bir əməliyyat başladın.
var customerToUpdate = customers.First();
customerToUpdate.Name = "Güncəllənmiş Ad";
context.Update(customerToUpdate);
context.SaveChanges();
```

---

## 4. Bölünmüş Sorğunu Bütün Sorğular Üçün Defolt Etmək

❌ **Yanlış İstifadə:** Bütün sorğularda Bölünmüş Sorğu istifadə edərək gərəksiz sorğular yaratmaq.

```csharp
optionsBuilder.UseSqlServer(connectionString, b => b.UseQuerySplittingBehavior(QuerySplittingBehavior.SplitQuery));
```

✅ **Düzgün İstifadə:** Bölünmüş Sorğunu yalnız müəyyən sorğular üçün istifadə edin.

```csharp
var data = context.Data
    .Include(d => d.RelatedData)
    .AsSplitQuery()
    .ToList();
```

---

## 5. Verilənlər Uyğunluğunu Göz Ardı Etmək

❌ **Yanlış İstifadə:** Bölünmüş Sorğu ilə verilənlər uyğunluğu problemlərinə səbəb olmaq.

```csharp
var orders = context.Orders
    .Include(o => o.Products)
    .AsSplitQuery()
    .ToList();

// Digər əməliyyatlar zamanı verilən dəyişə bilər.
```

✅ **Düzgün İstifadə:** Verilənlər uyğunluğu kritikdirsə, Bölünmüş Sorğu yerinə tək sorğu istifadə edin.

```csharp
var orders = context.Orders
    .Include(o => o.Products)
    .ToList();
```
