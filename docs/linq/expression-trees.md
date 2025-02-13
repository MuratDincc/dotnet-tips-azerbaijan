# Expression Trees ilə Dinamik LINQ Sorğuları

Expression Trees, C# dilində dinamik və mürəkkəb sorğular yaratmaq üçün güclü bir vasitədir. Xüsusilə LINQ ilə işləyərkən, **runtime** zamanı sorğuların yaradılması və optimallaşdırılması üçün istifadə olunur.

---

## 1. Expression Trees Nədir?

Expression Trees, kodunuzu bir ifadə ağacı formasında təmsil etməyə imkan verir. Bu metodla lambda ifadələrini analiz etmək və **runtime** zamanı dəyişikliklər etmək mümkündür.

**Əsas istifadələri:**
- **Dinamik Filtrləmə:** İstifadəçi sorğularına uyğun olaraq şərtlərin yaradılması.
- **Verilənlər Bazası Sorğuları:** LINQ to SQL və ya Entity Framework ilə dinamik sorğuların yaradılması.
- **Dərin Analiz:** Lambda ifadələrini araşdıraraq optimallaşdırmaq.

---

## 2. Səhv və Düzgün İstifadə

### **Səhv İstifadə:** Statik Şərtlər

❌ **Səhv İstifadə:**

```csharp
var filteredData = data.Where(x => x.Age > 32 && x.Name == "Murad").ToList();
```

Bu sorğu yalnız müəyyən bir şərt üçün sabitləşib və təkrar istifadəsi çətindir.

---

### **Düzgün İstifadə:** Dinamik Şərtlər

✅ **Düzgün İstifadə:**

```csharp
using System.Linq.Expressions;

Expression<Func<Person, bool>> filter = x => x.Age > 32 && x.Name == "Murad";
var filteredData = data.Where(filter.Compile()).ToList();
```

Bu metod, şərtləri **runtime** zamanı dinamik şəkildə yaratmağa imkan verir.

---

## 3. Dinamik Şərtlərin Yaradılması

Expression Trees ilə **runtime** zamanı şərtlər dinamik olaraq qurula bilər.

✅ **Misal:** İstifadəçi Məlumatlarına Görə Dinamik Sorğu

```csharp
var parameter = Expression.Parameter(typeof(Person), "x");
var property = Expression.Property(parameter, "Age");
var constant = Expression.Constant(32);
var comparison = Expression.GreaterThan(property, constant);

var lambda = Expression.Lambda<Func<Person, bool>>(comparison, parameter);

var filteredData = data.Where(lambda.Compile()).ToList();
```

Bu kod **runtime** zamanı `x => x.Age > 32` sorğusunu yaradır.

---

## 4. Çoxsaylı Şərtlərlə Dinamik Sorğu

Expression Trees ilə bir neçə şərt eyni anda dinamik şəkildə birləşdirilə bilər.

✅ **Misal:** Çoxsaylı Şərtlərin Birləşdirilməsi

```csharp
var parameter = Expression.Parameter(typeof(Person), "x");

var ageProperty = Expression.Property(parameter, "Age");
var ageCondition = Expression.GreaterThan(ageProperty, Expression.Constant(32));

var nameProperty = Expression.Property(parameter, "Name");
var nameCondition = Expression.Equal(nameProperty, Expression.Constant("Murad"));

var combinedCondition = Expression.AndAlso(ageCondition, nameCondition);

var lambda = Expression.Lambda<Func<Person, bool>>(combinedCondition, parameter);

var filteredData = data.Where(lambda.Compile()).ToList();
```

Bu kod **runtime** zamanı `x => x.Age > 32 && x.Name == "Murad"` sorğusunu qurur.

---

## 5. Dinamik Sıralama (Dynamic Ordering)

Expression Trees istifadə edərək sıralama əməliyyatlarını dinamik hala gətirmək mümkündür.

✅ **Misal:** Dinamik Sıralama

```csharp
var parameter = Expression.Parameter(typeof(Person), "x");
var property = Expression.Property(parameter, "Name");

var lambda = Expression.Lambda<Func<Person, string>>(property, parameter);

var sortedData = data.OrderBy(lambda.Compile()).ToList();
```

Bu kod şəxsləri adlarına görə sıralayır.

---

## 6. Performans və Optimizasiya

Expression Trees düzgün istifadə edildikdə performansı artırır, lakin lazımsız mürəkkəblik yaratmaqdan qaçmaq lazımdır.

**Tövsiyələr:**
- Tez-tez istifadə olunan sorğuları **compile** edərək yenidən istifadə edin.
- Çox mürəkkəb dinamik sorğuların performansını test edin və analiz edin.

✅ **Performans Təkmilləşdirmə Nümunəsi:**

```csharp
var compiledLambda = lambda.Compile();
// Compile edilmiş sorğunu təkrar istifadə edə bilərsiniz
var filteredData = data.Where(compiledLambda).ToList();
```
