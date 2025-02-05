# Toplu Əməliyyatlar

Entity Framework Core, verilənlər bazası əməliyyatları üçün güclü bir vasitədir, lakin böyük verilən toplularında tək-tək əməliyyat etmək yavaş və baha başa gələ bilər. Toplu əməliyyatlar (Bulk Operations) performansı artırmaq və əməliyyat müddətlərini azaltmaq üçün təsirli bir üsuldur. Lakin, yanlış istifadəsi verilənlər uyğunsuzluğuna və performans problemlərinə səbəb ola bilər.

---

## 1. Tək-Tək Əməliyyat Edərək Performansı Aşağı Salmaq

❌ **Yanlış İstifadə:** Böyük verilən kümələrində tək-tək əməliyyat etmək.

```csharp
foreach (var user in users)
{
    user.IsActive = true;
    context.Users.Update(user);
    context.SaveChanges();
}
```

✅ **Düzgün İstifadə:** Toplu update ilə bütün verilənləri tək bir əməliyyatda güncəlləyin.

```csharp
context.Users
    .Where(u => !u.IsActive)
    .ExecuteUpdate(u => u.SetProperty(x => x.IsActive, true));
```

---

## 2. Gərəksiz Verilənlər Bazası Triggerlərlə Çalışdırmaq

❌ **Yanlış İstifadə:** Toplu əməliyyatları triggerlərlə birləşdirmək.

```csharp
foreach (var product in products)
{
    product.StockCount += 10;
    context.Products.Update(product);
    context.SaveChanges(); // Hər əməliyyatda trigger çalışır
}
```

✅ **Düzgün İstifadə:** Toplu əməliyyatlar triggerlərlərdən asılı olmayaraq yerinə yetirilməlidir.

```csharp
context.Products
    .Where(p => p.StockCount > 0)
    .ExecuteUpdate(p => p.SetProperty(x => x.StockCount, x => x.StockCount + 10));
```

---

## 3. Gərəksiz Böyük Verilən Dəstlərini Yaddaşa Yükləmək

❌ **Yanlış İstifadə:** Verilənləri əvvəlcə yaddaşa yükləyib sonra güncəlləmək.

```csharp
var products = context.Products.ToList();
foreach (var product in products)
{
    product.Price += 5;
}
context.SaveChanges();
```

✅ **Düzgün İstifadə:** Yaddaşı səmərəli istifadə edərək birbaşa verilənlər bazası üzərində əməliyyat aparın.

```csharp
context.Products
    .Where(p => p.Price < 100)
    .ExecuteUpdate(p => p.SetProperty(x => x.Price, x => x.Price + 5));
```

---

## 4. Toplu Delete Əməliyyatlarında Yanlış Filtrləmə

❌ **Yanlış İstifadə:** Yanlış filtrlərlə gərəksiz verilənləri silmək.

```csharp
context.Products.RemoveRange(context.Products.Where(p => p.IsDiscontinued));
context.SaveChanges();
```

✅ **Düzgün İstifadə:** Toplu delete ilə birbaşa hədəflənən verini silin.

```csharp
context.Products
    .Where(p => p.IsDiscontinued)
    .ExecuteDelete();
```

---

## 5. Transaction İstifadəsini Nəzərə Almamaq

❌ **Yanlış İstifadə:** Böyük əməliyyatları transaction olmadan həyata keçirmək.

```csharp
context.Products
    .Where(p => p.CategoryId == 1)
    .ExecuteDelete();
context.Users
    .Where(u => u.IsInactive)
    .ExecuteUpdate(u => u.SetProperty(x => x.IsInactive, false));
```

✅ **Düzgün İstifadə:** Bütün əməliyyatları bir transaction içində yerinə yetirin.

```csharp
using var transaction = context.Database.BeginTransaction();
context.Products
    .Where(p => p.CategoryId == 1)
    .ExecuteDelete();
context.Users
    .Where(u => u.IsInactive)
    .ExecuteUpdate(u => u.SetProperty(x => x.IsInactive, false));
transaction.Commit();
```

---

## 6. Performans Optimizasiyası Üçün Xarici Kitabxanaları Göz Ardı Etmək

❌ **Yanlış İstifadə:** Performans üçün yalnız Entity Framework metodlarına güvənmək.

```csharp
context.BulkInsert(data); // EF-in daxili metodları
```

✅ **Düzgün İstifadə:** Performansı artırmaq üçün əlavə kitabxanalardan yararlanın.

```csharp
context.BulkInsert(data, options =>
{
    options.BatchSize = 1000;
    options.EnableStreaming = true;
});
```
