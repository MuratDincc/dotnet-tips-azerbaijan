# Async LINQ

Async LINQ əməliyyatları, xüsusilə verilənlər bazası sorğularında IO ilə bağlı prosesləri optimallaşdırmaq üçün istifadə olunur. Düzgün tətbiq edildikdə, performansı artıraraq istifadəçi təcrübəsini yaxşılaşdıra bilər. Lakin, səhv istifadə performans itkisinə və resursların səmərəsiz idarə olunmasına səbəb ola bilər.

---

## 1. Sinxron LINQ ilə Gecikmə Problemi

❌ **Səhv İstifadə:** Sinxron LINQ metodlarından istifadə edərək IO əməliyyatlarını bloklamaq.

```csharp
var users = context.Users
    .Where(u => u.IsActive)
    .ToList(); // Sinxron çağırış, bloklama yaradır.
```

✅ **Düzgün İstifadə:** Asinxron LINQ metodlarından istifadə edərək bloklamanı qarşısını alın.

```csharp
var users = await context.Users
    .Where(u => u.IsActive)
    .ToListAsync();
```

---

## 2. `ToListAsync` Metodunu Lazımsız İstifadə Etmək

❌ **Səhv İstifadə:** `ToListAsync` çağırışını lazımsız şəkildə başqa əməliyyatlarla istifadə etmək.

```csharp
var users = (await context.Users.ToListAsync())
    .Where(u => u.IsActive);
```

✅ **Düzgün İstifadə:** Filtrləri birbaşa asinxron sorğuya daxil edin.

```csharp
var users = await context.Users
    .Where(u => u.IsActive)
    .ToListAsync();
```

---

## 3. Hər Sətirdə `Await` İstifadə Edərək Performansı Azaltmaq

❌ **Səhv İstifadə:** Hər əməliyyatda ayrı `await` çağırışı etmək.

```csharp
var userList = new List<User>();
foreach (var userId in userIds)
{
    var user = await context.Users.FindAsync(userId);
    userList.Add(user);
}
```

✅ **Düzgün İstifadə:** Paralel işləmələri effektiv idarə etmək üçün `Task.WhenAll` istifadə edin.

```csharp
var tasks = userIds.Select(id => context.Users.FindAsync(id).AsTask());
var userList = await Task.WhenAll(tasks);
```

---

## 4. Asinxron Olmayan Verilər Üçün `ToListAsync` İstifadə Etmək

❌ **Səhv İstifadə:** Yaddaşda olan verilər üçün asinxron metodlardan istifadə etmək.

```csharp
var inMemoryList = new List<int> { 1, 2, 3 };
var result = await inMemoryList.ToListAsync(); // Yanlış istifadə
```

✅ **Düzgün İstifadə:** Yaddaşda saxlanılan verilər üçün standart LINQ metodlarından istifadə edin.

```csharp
var inMemoryList = new List<int> { 1, 2, 3 };
var result = inMemoryList.ToList();
```

---

## 5. `FirstAsync` və `SingleAsync` Metodlarını Yanlış İstifadə Etmək

❌ **Səhv İstifadə:** Bir neçə nəticə qaytara biləcək bir sorğu üçün `SingleAsync` istifadə etmək.

```csharp
var user = await context.Users.SingleAsync(u => u.IsActive); // Bir neçə nəticə döndərərsə, xəta baş verəcək
```

✅ **Düzgün İstifadə:** Bir neçə nəticənin qaytarıla biləcəyi hallarda `FirstOrDefaultAsync` istifadə edin.

```csharp
var user = await context.Users.FirstOrDefaultAsync(u => u.IsActive);
if (user == null)
{
    Console.WriteLine("İstifadəçi tapılmadı.");
}
```

---

## 6. `AsNoTracking` İstifadəsini Nəzərə Almamaq

❌ **Səhv İstifadə:** Sorğu nəticələrini yalnız oxuma məqsədilə istifadə edərkən izləmə (tracking) mexanizmini deaktiv etməmək.

```csharp
var products = await context.Products.ToListAsync(); // Tracking açıq qalır
```

✅ **Düzgün İstifadə:** Yalnız oxuma əməliyyatları üçün `AsNoTracking` istifadə edin.

```csharp
var products = await context.Products.AsNoTracking().ToListAsync();
```

Bu metodun istifadəsi yaddaş istifadəsini və performansı yaxşılaşdırır, xüsusilə də böyük verilənlər bazası sorğularında.