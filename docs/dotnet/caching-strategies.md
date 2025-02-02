# Keşləmə Strategiyaları

Keşləmə (caching), tətbiq performansını artırmağın və resurs istehlakını azaltmağın effektiv bir yoludur. Lakin, yanlış istifadə edilən keşləmə strategiyaları performans problemlərinə, verilənlərin uyğunsuzluğuna və həddindən artıq yaddaş istifadəsinə yol aça bilər.

---

## 1. Lazımsız Yerə Böyük Datanı Keşləmək

❌ **Yanlış İstifadə:** Böyük datanı birbaşa keşə əlavə etmək.

```csharp
var largeData = GetLargeData();
_memoryCache.Set("LargeData", largeData);
```

✅ **Düzgün İstifadə:** Böyük veriləri bölərək və ya sıxışdıraraq keşə əlavə edin.

```csharp
var largeData = GetLargeData();
var compressedData = CompressData(largeData);
_memoryCache.Set("CompressedLargeData", compressedData);
```

---

## 2. Keş Müddətini Yanlış Tənzimləmək

❌ **Yanlış İstifadə:** Sonsuz müddətlə keş istifadə etmək.

```csharp
_memoryCache.Set("Data", data, TimeSpan.MaxValue);
```

✅ **Düzgün İstifadə:** Uyğun bir müddət təyin edin və lazım gəldikdə sliding expiration istifadə edin.

```csharp
_memoryCache.Set("Data", data, new MemoryCacheEntryOptions
{
    AbsoluteExpirationRelativeToNow = TimeSpan.FromMinutes(30),
    SlidingExpiration = TimeSpan.FromMinutes(10)
});
```

---

## 3. Distributed Cache İstifadəsini Düşünməmək

❌ **Yanlış İstifadə:** Bütün keş verilənlərini yalnız yaddaş içində saxlamaq.

```csharp
services.AddMemoryCache();
```

✅ **Düzgün İstifadə:** Paylanmış keş istifadə edərək miqyaslanma qabiliyyətini artırın.

```csharp
services.AddStackExchangeRedisCache(options =>
{
    options.Configuration = "localhost:6379";
    options.InstanceName = "MyApp_";
});
```

---

## 4. Lazy Loading İstifadəsini Nəzərə Almamaq

❌ **Yanlış İstifadə:** Məlumatları əvvəlcədən yükləyib lazımsız yaddaş istifadəsi etmək.

```csharp
var data = GetDataFromDatabase();
_memoryCache.Set("Data", data);
```

✅ **Düzgün İstifadə:** Lazy loading ilə yalnız lazım olduqda məlumatları yükləyin.

```csharp
_memoryCache.GetOrCreate("Data", entry =>
{
    entry.AbsoluteExpirationRelativeToNow = TimeSpan.FromMinutes(30);
    return GetDataFromDatabase();
});
```

---

## 5. Keş İnvaildasiyasını İdarə Edə Bilməmək

❌ **Yanlış İstifadə:** Verilən dəyişikliklərini keşə əks etdirməmək.

```csharp
_memoryCache.Set("User_123", user);
user.Name = "Updated Name"; // Keş yenilənmir
```

✅ **Düzgün İstifadə:** Verilən dəyişikliklərini keşdə yeniləyin və ya invaildasiyanı idarə edin.

```csharp
_memoryCache.Remove("User_123");
_memoryCache.Set("User_123", updatedUser);
```

---

## 6. Həssas Məlumatların Keş Edilməsi

❌ **Yanlış İstifadə:** Şifrələri və ya şəxsi məlumatları keşə əlavə etmək.

```csharp
_memoryCache.Set("UserPassword", "123456");
```

✅ **Düzgün İstifadə:** Həssas məlumatları keşə əlavə etməkdən qaçının.

---

## 7. Keş İstifadəsini Ölçməməmək

❌ **Yanlış İstifadə:** Cache hit/miss nisbətlərini izləməmək.

✅ **Düzgün İstifadə:** Keş performansını izləmək üçün ölçmə aparın.

- **Nümunə:** Prometheus, Grafana və ya App Insights istifadə edərək performans verilənlərini toplayın.
- **Kodda Nümunə:**

```csharp
if (!_memoryCache.TryGetValue("Data", out var data))
{
    data = GetDataFromDatabase();
    _memoryCache.Set("Data", data);
    Console.WriteLine("Cache miss!");
}
else
{
    Console.WriteLine("Cache hit!");
}
```
