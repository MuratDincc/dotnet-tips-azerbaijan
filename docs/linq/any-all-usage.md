# LINQ ilə Any və All İstifadəsi

`Any` və `All`, LINQ sorğularında kolleksiyalar üzərində müəyyən bir şərtin ödənilib-ödənilmədiyini yoxlamaq üçün istifadə olunan iki güclü metoddur. Düzgün istifadə edildikdə performansı və oxunaqlılığı artırırlar, lakin səhv istifadələri lazımsız əməliyyatlara səbəb ola bilər.

---

## 1. Any və All Nədir?

- **Any:** Kolleksiyadakı hər hansı bir elementin müəyyən bir şərti ödəyib-ödəmədiyini yoxlayır.
- **All:** Kolleksiyadakı bütün elementlərin müəyyən bir şərti ödəyib-ödəmədiyini yoxlayır.

**İstifadə nümunəsi:**

```csharp
var hasAdults = people.Any(p => p.Age >= 18); // Hər hansı bir şəxs yetkindirmi?
var allAdults = people.All(p => p.Age >= 18); // Bütün şəxslər yetkindirmi?
```

---

## 2. Səhv və Düzgün İstifadə

### **Səhv İstifadə:** Döngü ilə yoxlama

❌ **Səhv İstifadə:**

```csharp
bool hasAdults = false;
foreach (var person in people)
{
    if (person.Age >= 18)
    {
        hasAdults = true;
        break;
    }
}
```

Bu üsul `Any` metodu əvəzinə lazımsız bir döngü istifadə edir və kodun oxunaqlığını azaldır.

---

### **Düzgün İstifadə:** `Any` metodu ilə yoxlama

✅ **Düzgün İstifadə:**

```csharp
var hasAdults = people.Any(p => p.Age >= 18);
```

Bu üsul, kolleksiyanın ilk uyğun elementini tapdıqda prosesi dayandırır və daha səmərəlidir.

---

## 3. Any və All İstifadə Sahələri

### **1. Boş Kolleksiya Yoxlamaq**

`Any` metodu kolleksiyanın boş olub-olmadığını yoxlamaq üçün istifadə edilə bilər.

✅ **Nümunə:**

```csharp
if (!people.Any())
{
    Console.WriteLine("Kolleksiya boşdur.");
}
```

### **2. Bütün Elementləri Yoxlamaq**

`All` metodu kolleksiyadakı bütün elementlərin bir şərti ödəyib-ödəmədiyini yoxlayır.

✅ **Nümunə:**

```csharp
var allActive = users.All(u => u.IsActive);
```

---

## 4. Performans Məsləhətləri

- **Any**: İlk uyğun elementi tapdıqdan sonra prosesi dayandırır, buna görə də böyük kolleksiyalarda sürətlidir.
- **All**: Bütün elementləri yoxladığı üçün böyük kolleksiyalarda daha yavaş ola bilər.

✅ **Performans Müqayisəsi Nümunəsi:**

```csharp
var stopwatch = Stopwatch.StartNew();

// Any ilə yoxlama
var hasAdults = people.Any(p => p.Age >= 18);
stopwatch.Stop();
Console.WriteLine($"Any Müddəti: {stopwatch.ElapsedMilliseconds} ms");

stopwatch.Restart();

// All ilə yoxlama
var allAdults = people.All(p => p.Age >= 18);
stopwatch.Stop();
Console.WriteLine($"All Müddəti: {stopwatch.ElapsedMilliseconds} ms");
```

---

## 5. Dinamik Şərtlərlə Any və All

Şərtləri işləmə zamanı (`runtime`) dinamik şəkildə təyin edə bilərsiniz.

✅ **Nümunə:**

```csharp
Func<Person, bool> isAdult = p => p.Age >= 18;

var hasAdults = people.Any(isAdult);
var allAdults = people.All(isAdult);
```

Bu üsul kodunuzu daha çevik və oxunaqlı edir.