# Async Streams

Async stream-lər data'nı asinxron axın şəklində işləmək üçün güclü bir vasitədir. Düzgün istifadə edildikdə performansı artırır və kodunuzu daha səmərəli edir. Ancaq, yanlış istifadələr performans problemlərinə və gözlənilməz davranışlara səbəb ola bilər.

---

## 1. Asinxron Axınların Yanlış İstifadəsi

❌ **Yanlış İstifadə:** Bütün data'nı yaddaşda saxlayaraq işləmək.

```csharp
var data = await GetDataAsync();
foreach (var item in data)
{
    Console.WriteLine(item);
}
```

✅ **Düzgün İstifadə:** Asinxron data'nı `await foreach` vasitəsilə istifadə etmək.

```csharp
await foreach (var item in GetDataAsync())
{
    Console.WriteLine(item);
}
```

---

## 2. Xətaların İdarəsini Nəzərə Almamaq

❌ **Yanlış İstifadə:** Asinxron axınlar zamanı xətaları gözardı etmək.

```csharp
await foreach (var data in GetDataAsync())
{
    ProcessData(data); // Xətaları nəzərə almır
}
```

✅ **Düzgün İstifadə:** Xətaları `try-catch` bloku ilə idarə etmək.

```csharp
try
{
    await foreach (var data in GetDataAsync())
    {
        ProcessData(data);
    }
}
catch (Exception ex)
{
    Console.WriteLine($"Xəta: {ex.Message}");
}
```

---

## 3. Performansı Optimallaşdırmamaq

❌ **Yanlış İstifadə:** Bütün elementləri eyni anda işləmək.

```csharp
await foreach (var item in GetLargeDataAsync())
{
    ProcessItem(item);
}
```

✅ **Düzgün İstifadə:** Axını erkən dayandırmağın mümkün olduğu hallarda dövrəni vaxtında sonlandırmaq.

```csharp
await foreach (var item in GetLargeDataAsync())
{
    if (ShouldStopProcessing(item)) break;
    ProcessItem(item);
}
```

---

## 4. `CancellationToken` İstifadəsini Nəzərə Almamaq

❌ **Yanlış İstifadə:** Asinxron axınlarda ləğv dəstəyini gözardı etmək.

```csharp
await foreach (var item in GetDataAsync())
{
    Console.WriteLine(item);
}
```

✅ **Düzgün İstifadə:** `CancellationToken` istifadə edərək əməliyyatın ləğvini təmin etmək.

```csharp
await foreach (var item in GetDataAsync().WithCancellation(cancellationToken))
{
    Console.WriteLine(item);
}
```

---

## 5. Lazımsız Verilərin Filtrlənməsi

❌ **Yanlış İstifadə:** Axından alınan məlumatları dövrənin daxilində filtrasiya etmək.

```csharp
await foreach (var item in GetDataAsync())
{
    if (item.IsRelevant)
    {
        Console.WriteLine(item);
    }
}
```

✅ **Düzgün İstifadə:** Axın zamanı veriləri filtrləməyi optimallaşdırmaq.

```csharp
await foreach (var item in GetFilteredDataAsync())
{
    Console.WriteLine(item);
}
```

---

## 6. Müstəqil Əməliyyatları Sinxron İcra Etmək

❌ **Yanlış İstifadə:** Müstəqil əməliyyatları ardıcıl şəkildə icra etmək.

```csharp
await foreach (var item in GetDataAsync())
{
    await ProcessItemAsync(item); // Müstəqil əməliyyatlar ardıcıl işləyir
}
```

✅ **Düzgün İstifadə:** Əməliyyatları paralel icra etmək üçün `Task.WhenAll` istifadə edin.

```csharp
var tasks = new List<Task>();
await foreach (var item in GetDataAsync())
{
    tasks.Add(ProcessItemAsync(item));
}
await Task.WhenAll(tasks);
```