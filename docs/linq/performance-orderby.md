### LINQ ilə Performanslı Sıralama: `OrderBy` İstifadəsi

Böyük verilər üzərində **sıralama əməliyyatları** (sorting) performansa ciddi təsir edə bilər. LINQ-də **`OrderBy` və `ThenBy` metodları** istifadə olunaraq sıralama prosesi effektiv şəkildə həyata keçirilə bilər.

---

## 1. `OrderBy` və `ThenBy` Nədir?

- **`OrderBy`** → Məlumatı verilən sütuna görə **artan sırada** sıralayır.
- **`ThenBy`** → Əsas sıralamadan sonra **ikinci dərəcəli** bir sıralama tətbiq edir.

✅ **İstifadə Nümunəsi:**

```csharp
var sortedData = data
    .OrderBy(x => x.Name)
    .ThenBy(x => x.Age)
    .ToList();
```

🔹 **Bu kodun işi:**  
1. **`Name`** sütununa görə artan sıralama aparır.  
2. **`Name`** dəyərləri eyni olduqda, **`Age`** sütununa görə sıralayır.

---

## 2. Səhv və Düzgün İstifadə

### **Səhv İstifadə:** `OrderBy` metodunu bir neçə dəfə çağırmaq

❌ **Səhv İstifadə:**

```csharp
var sortedData = data
    .OrderBy(x => x.Name)
    .OrderBy(x => x.Age) // İkinci OrderBy birincini əvəz edir!
    .ToList();
```

🔹 **Problem:**  
Hər `OrderBy` çağırışı əvvəlki sıralamanı ləğv edir, nəticədə **yalnız son `OrderBy` işləyir**.

---

### **Düzgün İstifadə:** `ThenBy` ilə birləşdirilmiş sıralama

✅ **Düzgün İstifadə:**

```csharp
var sortedData = data
    .OrderBy(x => x.Name)
    .ThenBy(x => x.Age)
    .ToList();
```

🔹 **Nəticə:**  
İlk `OrderBy` **əsas sıralamanı** edir, `ThenBy` isə **ikinci dərəcəli sıralamanı** təmin edir.

---

## 3. Sıralama Performansını Artırmaq

Böyük verilər üzərində işləyərkən performansı optimallaşdırmaq üçün **aşağıdakı texnikalardan istifadə edin**.

### **1. `AsQueryable()` ilə Sorğunu Verilənlər Bazasında İcra Etmək**

LINQ sorğularını **verilənlər bazasında icra etmək**, onları yaddaşda işlətməkdən daha sürətlidir.

✅ **Misal:**

```csharp
var sortedData = context.Customers
    .AsQueryable()
    .OrderBy(x => x.Name)
    .ToList();
```

🔹 **Fayda:**  
- **Sıralama verilənlər bazasında icra olunur,** yalnız lazımi məlumat RAM-ə gətirilir.

---

### **2. Verilənlər Bazasında İndekslərdən İstifadə Etmək**

**İndekslər (`INDEX`)** sıralama əməliyyatlarını sürətləndirir.

✅ **SQL ilə İndeks Yaratmaq:**

```sql
CREATE INDEX idx_name ON Customers (Name);
```

🔹 **Fayda:**  
- Sıralama aparılan sütun **indeksləndikdə**, verilənlər bazası sıralama prosesini daha sürətli icra edir.

---

### **3. Azalan Sıralama (`Descending Order`)**

`OrderByDescending` metodu **artan** deyil, **azalan** (descending) sıralama aparır.

✅ **Misal:**

```csharp
var sortedData = data.OrderByDescending(x => x.Date).ToList();
```

🔹 **Bu kod `Date` sütununa görə azalan sıralama aparır.**

---

## 4. Dinamik Sıralama (`Dynamic Sorting`)

İstifadəçi tərəfindən seçilən sütuna əsasən dinamik sıralama aparmaq olar.

✅ **Misal:**

```csharp
public List<T> SortData<T>(IQueryable<T> query, string sortColumn, bool ascending)
{
    var parameter = Expression.Parameter(typeof(T), "x");
    var property = Expression.Property(parameter, sortColumn);
    var lambda = Expression.Lambda<Func<T, object>>(
        Expression.Convert(property, typeof(object)), parameter
    );

    return ascending
        ? query.OrderBy(lambda).ToList()
        : query.OrderByDescending(lambda).ToList();
}

// Metodun istifadəsi:
var sortedCustomers = SortData(context.Customers, "Name", true);
```

🔹 **Nəticə:**  
- **İstifadəçi dinamik olaraq istədiyi sütunu seçir.**
- **Artan və ya azalan sıralama seçimi mümkündür.**

---

## 5. Çoxsaylı Sütunlarla Sıralama (`Multiple Column Sorting`)

**Məlumatları bir neçə sütuna əsasən sıralamaq üçün `ThenBy` və `ThenByDescending` istifadə edilir.**

✅ **Misal:**

```csharp
var sortedData = data
    .OrderBy(x => x.LastName)
    .ThenByDescending(x => x.FirstName)
    .ToList();
```

🔹 **Bu kod necə işləyir?**  
1. **Əvvəlcə `LastName` sütununa görə artan sıralama aparır.**  
2. **Əgər `LastName` eyni olarsa, `FirstName`-ə görə azalan sıralama edir.**

---

## 6. Performans Testi (`Performance Benchmarking`)

**Sıralama performansını ölçmək üçün `Stopwatch` istifadə edin.**

✅ **Misal:**

```csharp
var stopwatch = Stopwatch.StartNew();

// Sıralama icra edilir
var sortedData = data.OrderBy(x => x.Name).ToList();

stopwatch.Stop();
Console.WriteLine($"Sıralama vaxtı: {stopwatch.ElapsedMilliseconds} ms");
```

🔹 **Bu yanaşma real dünya tətbiqlərində performansı izləmək üçün faydalıdır.**

---

## **Nəticə və Tövsiyələr**

| **Metod**             | **İstifadə Halı**                                              |
|----------------------|--------------------------------------------------------------|
| **OrderBy**         | Verilənlər **artan sırada** sıralandıqda.                     |
| **OrderByDescending** | Verilənlər **azalan sırada** sıralandıqda.                   |
| **ThenBy**          | **İkinci dərəcəli** artan sıralama üçün.                      |
| **ThenByDescending** | **İkinci dərəcəli** azalan sıralama üçün.                    |
| **AsQueryable**     | **Verilənlər bazasında sıralama icra etmək** üçün.            |
| **İndekslər (`INDEX`)** | **Sıralama performansını artırmaq** üçün.                   |
| **Dinamik Sıralama** | İstifadəçi tərəfindən **dinamik sütun seçimi** tələb olunanda. |

---
🚀 **Əsas Qaydalar:**
- **Sıralama verilənlər bazasında (`AsQueryable`) icra olunmalıdır.**
- **Bir neçə `OrderBy` çağırışı yerinə `ThenBy` istifadə edin.**
- **İndekslər (`INDEX`) yaradaraq verilənlər bazası performansını artırın.**
- **Sıralama performansını ölçmək üçün `Stopwatch` istifadə edin.**
