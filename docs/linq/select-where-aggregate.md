# `Select`, `Where` və `Aggregate`  

LINQ sorğuları, kolleksiyalar və verilənlər bazası əməliyyatları üzərində effektiv şəkildə məlumat işləmək üçün güclü bir alətdir. Lakin, `Select`, `Where` və `Aggregate` kimi LINQ metodlarının səhv istifadəsi performans itkisinə və mürəkkəb kod quruluşuna səbəb ola bilər.  

---

## 1. Lazımsız `Select` İstifadəsi  

❌ **Yanlış İstifadə:** Lazım olmayan məlumat projeksiyaları etmək.  

```csharp
var productNames = context.Products
    .Select(p => new { p.Name, p.Price })
    .Select(p => p.Name)
    .ToList();
```

✅ **Düzgün İstifadə:** Lazımsız `Select` projeksiyalarından çəkinin.  

```csharp
var productNames = context.Products
    .Select(p => p.Name)
    .ToList();
```

---

## 2. `Where` ilə Mürəkkəb Filtrlər Yazmaq  

❌ **Yanlış İstifadə:** `Where` daxilində mürəkkəb məntiq istifadə edərək oxunaqlılığı azaltmaq.  

```csharp
var expensiveProducts = context.Products
    .Where(p => p.Price > 100 && p.Stock > 0 && p.Category == "Electronics")
    .ToList();
```

✅ **Düzgün İstifadə:** Filtrləri köməkçi metodlarla bölərək oxunaqlılığı artırın.  

```csharp
var expensiveProducts = context.Products
    .Where(IsExpensiveAndInStock)
    .ToList();

bool IsExpensiveAndInStock(Product product) =>
    product.Price > 100 && product.Stock > 0 && product.Category == "Electronics";
```

---

## 3. `Aggregate` Metodunu Səhv İstifadə Etmək  

❌ **Yanlış İstifadə:** Sadə toplama və ya birləşdirmə əməliyyatları üçün `Aggregate` istifadə etmək.  

```csharp
var totalStock = context.Products
    .Select(p => p.Stock)
    .Aggregate(0, (acc, stock) => acc + stock);
```

✅ **Düzgün İstifadə:** Sadə əməliyyatlar üçün uyğun LINQ metodlarından istifadə edin.  

```csharp
var totalStock = context.Products.Sum(p => p.Stock);
```

---

## 4. Performansı Nəzərə Almamaq  

❌ **Yanlış İstifadə:** Filtrləri verilənlər bazası əvəzinə yaddaşda icra etmək.  

```csharp
var products = context.Products.ToList();
var expensiveProducts = products.Where(p => p.Price > 100);
```

✅ **Düzgün İstifadə:** Filtrləmə əməliyyatlarını verilənlər bazasında icra edin.  

```csharp
var expensiveProducts = context.Products
    .Where(p => p.Price > 100)
    .ToList();
```

---

## 5. Çox Mərhələli Sorğuların Mürəkkəbləşdirilməsi  

❌ **Yanlış İstifadə:** Bir neçə LINQ zənciri ilə mürəkkəb sorğular yazmaq.  

```csharp
var productData = context.Products
    .Where(p => p.Price > 100)
    .Select(p => new { p.Name, p.Stock })
    .Where(p => p.Stock > 10)
    .ToList();
```

✅ **Düzgün İstifadə:** Sorğuları aydın və tək bir zəncir kimi yazın.  

```csharp
var productData = context.Products
    .Where(p => p.Price > 100 && p.Stock > 10)
    .Select(p => new { p.Name, p.Stock })
    .ToList();
```

---

## 6. `First` və `Single` Metodlarını Yanlış İstifadə Etmək  

❌ **Yanlış İstifadə:** `First` və ya `Single` metodlarını istifadə edərək xəta riski yaratmaq.  

```csharp
var product = context.Products.Single(p => p.Id == 1); // Məlumat yoxdursa xəta verir.
```

✅ **Düzgün İstifadə:** `FirstOrDefault` və ya `SingleOrDefault` metodlarından istifadə edərək təhlükəsiz sorğu icra edin.  

```csharp
var product = context.Products.SingleOrDefault(p => p.Id == 1);
if (product == null)
{
    Console.WriteLine("Məhsul tapılmadı.");
}
```
