# Dəyər Çevirmələri (Value Conversions)

Entity Framework Core-da Dəyər Çevirmələri, bir entitet xüsusiyyətinin Məlumatlənlər bazasında fərqli bir formatda saxlanmasını təmin edir. Bu xüsusiyyət, xüsusi tipləri dəstəkləmək və elastiklik təmin etmək üçün olduqca faydalıdır. Lakin, yanlış istifadəsi məlumat itkilərinə və performans problemlərinə səbəb ola bilər.

---

## 1. Gərəksiz Dəyər Çevirici İstifadəsi

❌ **Yanlış İstifadə:** Sadə tiplər üçün gərəksiz Dəyər Çevirici təyin etmək.

```csharp
modelBuilder.Entity<Product>()
    .Property(p => p.Price)
    .HasConversion(
        v => v.ToString(),
        v => decimal.Parse(v));
```

✅ **Düzgün İstifadə:** Dəyər Çeviricini yalnız lazım olduqda təyin edin.

```csharp
public class Product
{
    public decimal Price { get; set; }
}
```

---

## 2. Məlumat İtkilərini Göz Ardı Etmək

❌ **Yanlış İstifadə:** Çevirmə zamanı məlumat itkisini göz ardı etmək.

```csharp
modelBuilder.Entity<Product>()
    .Property(p => p.Rating)
    .HasConversion(
        v => (int)v,  // Məlumat itkisi riski
        v => (double)v);
```

✅ **Düzgün İstifadə:** Çevirmənin məlumat bütövlüyünü qoruyacaq şəkildə edilməsi.

```csharp
modelBuilder.Entity<Product>()
    .Property(p => p.Rating)
    .HasConversion(
        v => Math.Round(v, 2),  // Hassaslıq qorunur
        v => v);
```

---

## 3. Mürəkkəb Çevirmələri Property Səviyyəsində Etmək

❌ **Yanlış İstifadə:** Mürəkkəb çevirmələri Dəyər Çevirici içində etmək.

```csharp
modelBuilder.Entity<User>()
    .Property(u => u.Roles)
    .HasConversion(
        v => string.Join(",", v),  // Mürəkkəb çevirmə
        v => v.Split(','));
```

✅ **Düzgün İstifadə:** Mürəkkəb çevirmələri ayrı bir sinif və ya metodla idarə etmək.

```csharp
public class RoleConverter : ValueConverter<List<string>, string>
{
    public RoleConverter()
        : base(
            v => string.Join(",", v),
            v => v.Split(',').ToList())
    {
    }
}

modelBuilder.Entity<User>()
    .Property(u => u.Roles)
    .HasConversion(new RoleConverter());
```

---

## 4. Tarix və Saat Çevirmələrində Yanlış Format İstifadəsi

❌ **Yanlış İstifadə:** Tarix və saat çevirmələrində standart formatı istifadə etməmək.

```csharp
modelBuilder.Entity<Order>()
    .Property(o => o.OrderDate)
    .HasConversion(
        v => v.ToString(),
        v => DateTime.Parse(v)); // Format fərqləri problem yarada bilər
```

✅ **Düzgün İstifadə:** Tarix və saat çevirmələrində `DateTimeOffset` istifadə etmək.

```csharp
modelBuilder.Entity<Order>()
    .Property(o => o.OrderDate)
    .HasConversion(
        v => v.ToString("o"),
        v => DateTimeOffset.Parse(v));
```

---

## 5. Performans Problemlərini Göz Ardı Etmək

❌ **Yanlış İstifadə:** Böyük məlumat toplularında performans problemlərini göz ardı etmək.

```csharp
modelBuilder.Entity<Log>()
    .Property(l => l.Details)
    .HasConversion(
        v => JsonConvert.SerializeObject(v),
        v => JsonConvert.DeserializeObject<Dictionary<string, string>>(v)); 
```

✅ **Düzgün İstifadə:** Performansı optimallaşdıran daha sürətli çevirmələr istifadə etmək.

```csharp
modelBuilder.Entity<Log>()
    .Property(l => l.Details)
    .HasConversion(
        v => System.Text.Json.JsonSerializer.Serialize(v),
        v => System.Text.Json.JsonSerializer.Deserialize<Dictionary<string, string>>(v));
```

---

## 6. Test Edilə Bilmə Qabiliyyətini Göz Ardı Etmək

❌ **Yanlış İstifadə:** Çevirmə məntiqini test edilə bilən hala gətirməmək.

```csharp
modelBuilder.Entity<User>()
    .Property(u => u.Preferences)
    .HasConversion(
        v => string.Join(";", v),
        v => v.Split(';').ToList());
```

✅ **Düzgün İstifadə:** Çevirmə məntiqini test edilə bilən hala gətirmək üçün ayrı bir sinif istifadə etmək.

```csharp
public class PreferencesConverter : ValueConverter<List<string>, string>
{
    public PreferencesConverter()
        : base(
            v => string.Join(";", v),
            v => v.Split(';').ToList())
    {
    }
}

modelBuilder.Entity<User>()
    .Property(u => u.Preferences)
    .HasConversion(new PreferencesConverter());
```
