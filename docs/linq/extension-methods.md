Here is your translated text from Turkish to Azerbaijani while maintaining technical accuracy and logical flow:

---

# LINQ Genişlənmə (Extension) Metodları  

LINQ (Language Integrated Query), C#-da məlumat emalını asanlaşdırmaq üçün güclü bir alətdir. LINQ, genişlənmə metodları (`extension methods`) vasitəsilə sorğu yazımını daha çevik və oxunaqlı edir. Lakin, bu metodların səhv istifadəsi performans itkisinə və mürəkkəb kod strukturlarına səbəb ola bilər.  

---



## 1. Lazımsız `ToList` İstifadəsi  

❌ **Yanlış İstifadə:** Sorğunun hər addımında `ToList` istifadə etmək.  

```csharp
var filteredData = context.Data
    .Where(d => d.IsActive)
    .ToList()
    .Select(d => d.Name)
    .ToList();
```

✅ **Düzgün İstifadə:** Sorğunu tək əməliyyatda icra edin.  

```csharp
var filteredData = context.Data
    .Where(d => d.IsActive)
    .Select(d => d.Name)
    .ToList();
```

---

## 2. Böyük Məlumat Dəstlərində `OrderBy` ilə Performans Problemləri  

❌ **Yanlış İstifadə:** Sıralamanı yaddaşda (`memory`) aparmaq.  

```csharp
var data = context.Data.ToList().OrderBy(d => d.Name).ToList();
```

✅ **Düzgün İstifadə:** Sıralama əməliyyatını verilənlər bazasında icra edin.  

```csharp
var data = context.Data
    .OrderBy(d => d.Name)
    .ToList();
```

---

## 3. Lazımsız `Select` İstifadəsi  

❌ **Yanlış İstifadə:** Lazımsız projeksiyalar (`projection`) etmək.  

```csharp
var data = context.Data
    .Select(d => new { d.Id, d.Name })
    .Select(d => d.Name)
    .ToList();
```

✅ **Düzgün İstifadə:** Birbaşa ehtiyac duyulan məlumatı seçin.  

```csharp
var data = context.Data
    .Select(d => d.Name)
    .ToList();
```

---

## 4. `First` və `Single` Metodlarını Yanlış İstifadə Etmək  

❌ **Yanlış İstifadə:** Məlumat tapılmadıqda xəta yaradan `First` və ya `Single` metodlarını istifadə etmək.  

```csharp
var item = context.Data.First(d => d.Id == 1); // Məlumat yoxdursa xəta atır
```

✅ **Düzgün İstifadə:** Təhlükəsiz sorğular üçün `FirstOrDefault` və ya `SingleOrDefault` istifadə edin.  

```csharp
var item = context.Data.FirstOrDefault(d => d.Id == 1);
if (item == null)
{
    Console.WriteLine("Məlumat tapılmadı.");
}
```

---

## 5. `Count` İstifadəsi ilə Performansa Mənfi Təsir Etmək  

❌ **Yanlış İstifadə:** `Count` metodunu yaddaşdakı bir kolleksiyaya tətbiq etmək.  

```csharp
var count = context.Data.ToList().Count;
```

✅ **Düzgün İstifadə:** `Count` əməliyyatını birbaşa verilənlər bazasında icra edin.  

```csharp
var count = context.Data.Count();
```

---

## 6. Genişlənmə Metodları ilə Mürəkkəb Strukturlar Yazmaq  

❌ **Yanlış İstifadə:** Tək sətirdə mürəkkəb əməliyyatları zəncirləmək.  

```csharp
var data = context.Data
    .Where(d => d.IsActive)
    .OrderBy(d => d.Name)
    .Select(d => new { d.Id, d.Name, d.Date })
    .ToList()
    .GroupBy(d => d.Date.Year);
```

✅ **Düzgün İstifadə:** Əməliyyatları mərhələlərə bölərək kodun oxunaqlılığını artırın.  

```csharp
var activeData = context.Data
    .Where(d => d.IsActive)
    .OrderBy(d => d.Name)
    .Select(d => new { d.Id, d.Name, d.Date })
    .ToList();

var groupedData = activeData.GroupBy(d => d.Date.Year);
```

---

## 7. `Any` və `Exists` Metodlarının İstifadəsini Nəzərə Almamaq  

❌ **Yanlış İstifadə:** Mövcudluğu yoxlamaq üçün `Count` metodundan istifadə etmək.  

```csharp
var exists = context.Data.Count(d => d.IsActive) > 0;
```

✅ **Düzgün İstifadə:** Daha performanslı `Any` metodunu istifadə edin.  

```csharp
var exists = context.Data.Any(d => d.IsActive);
```