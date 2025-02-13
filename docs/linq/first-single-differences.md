# LINQ: First və Single Fərqləri

LINQ-də `First`, `FirstOrDefault`, `Single` və `SingleOrDefault` metodları, kolleksiyalardan müəyyən bir elementi seçmək üçün istifadə olunur. Lakin bu metodların səhv istifadəsi performans problemlərinə və gözlənilməz xətalara səbəb ola bilər.

---

## 1. First və Single Nədir?

### **First**
- Kolleksiyadakı ilk elementi qaytarır.
- Əgər element tapılmazsa, `InvalidOperationException` istisnası atır.
- İlk element tapılan kimi prosesi dayandırır, bu da sürətli işləməsinə səbəb olur.

**Misal:**

```csharp
var firstCustomer = customers.First(c => c.IsActive);
Console.WriteLine(firstCustomer.Name);
```

---

### **Single**
- Kolleksiyada **yalnız bir** element varsa, həmin elementi qaytarır.
- Əgər bir neçə uyğun element varsa, `InvalidOperationException` istisnası atır.
- Əgər uyğun element tapılmazsa, yenə `InvalidOperationException` atır.

**Misal:**

```csharp
var singleCustomer = customers.Single(c => c.Id == 1);
Console.WriteLine(singleCustomer.Name);
```

---

## 2. Səhv və Düzgün İstifadə

### **Səhv İstifadə:** `First` ilə unikal element yoxlamaq

❌ **Səhv İstifadə:**

```csharp
var singleCustomer = customers.First(c => c.Id == 1);
```

Bu kod kolleksiyanın yalnız bir element ehtiva etdiyini yoxlamır və gözlənilməz nəticələrə səbəb ola bilər.

---

### **Düzgün İstifadə:** `Single` ilə unikal element yoxlamaq

✅ **Düzgün İstifadə:**

```csharp
var singleCustomer = customers.Single(c => c.Id == 1);
```

Bu metod kolleksiyada yalnız bir element olduğuna **zəmanət verir**.

---

## 3. Default Dəyər Dəstəyi

Əgər kolleksiyada elementin olmaması ehtimalını nəzərə alırsınızsa, `FirstOrDefault` və ya `SingleOrDefault` metodlarından istifadə edə bilərsiniz.

✅ **Misal:**

```csharp
var firstCustomer = customers.FirstOrDefault(c => c.IsActive);
if (firstCustomer != null)
{
    Console.WriteLine(firstCustomer.Name);
}
```

```csharp
var singleCustomer = customers.SingleOrDefault(c => c.Id == 1);
if (singleCustomer != null)
{
    Console.WriteLine(singleCustomer.Name);
}
```

---

## 4. Performans Fərqləri

- **First:** İlk uyğun elementi tapdıqdan sonra prosesi dayandırır, buna görə **daha sürətlidir**.
- **Single:** Kolleksiyanı **bütövlükdə skan edir**, çünki unikal elementi yoxlamalıdır və bu səbəbdən daha **yavaşdır**.

✅ **Performans Müqayisəsi:**

```csharp
var stopwatch = Stopwatch.StartNew();

// First istifadəsi
var firstCustomer = customers.First(c => c.IsActive);
stopwatch.Stop();
Console.WriteLine($"First Vaxtı: {stopwatch.ElapsedMilliseconds} ms");

stopwatch.Restart();

// Single istifadəsi
var singleCustomer = customers.Single(c => c.Id == 1);
stopwatch.Stop();
Console.WriteLine($"Single Vaxtı: {stopwatch.ElapsedMilliseconds} ms");
```

---

## 5. Hansı Halda Hansı Metod İstifadə Olunmalıdır?

| **Metod**         | **İstifadə Halları**                                             |
|--------------------|-----------------------------------------------------------------|
| **First**         | İlk uyğun elementi almaq istədikdə.                              |
| **FirstOrDefault**| Əgər element yoxdursa, `null` və ya default dəyər qaytarmaq üçün. |
| **Single**        | Kolleksiyada **yalnız bir** element olduğuna əmin olduğunuzda.   |
| **SingleOrDefault**| Kolleksiyada **maksimum bir** element olarsa qaytarmaq üçün.    |

Bu cədvələ əsasən, **hansı metodun hansı vəziyyətdə istifadə olunacağını asanlıqla seçə bilərsiniz**.