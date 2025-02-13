# LINQ ilə Səhifələmə: Take və Skip İstifadəsi

Böyük verilər üzərində işləyərkən **məlumatın müəyyən hissəsini almaq** və ya **səhifələmə** ilə məlumatları gətirmək geniş yayılmış bir ehtiyacdır. **LINQ-də `Take` və `Skip` metodları** bu ehtiyacı qarşılamaq üçün istifadə olunur.

---

## 1. Take və Skip Nədir?

- **`Take(n)`** → Verilən sayda elementi götürür.
- **`Skip(n)`** → Verilən sayda elementi ötürür.

Bu iki metod birləşdirildikdə səhifələmə (pagination) **effektiv və performanslı şəkildə** həyata keçirilə bilər.

---

## 2. Səhv və Düzgün İstifadə

### **Səhv İstifadə:** Bütün veriləri əvvəlcə gətirmək

❌ **Səhv İstifadə:**

```csharp
var allData = data.ToList(); // Bütün veriləri yaddaşa yükləyir
var pageData = allData.Skip(10).Take(10).ToList(); // Lazımsız yaddaş istifadəsi
```

Bu yanaşma **lazımsız yerə bütün veriləri yaddaşa gətirir** və performans itkisinə səbəb olur.

---

### **Düzgün İstifadə:** `Take` və `Skip` metodlarını **birbaşa sorğuda istifadə etmək**

✅ **Düzgün İstifadə:**

```csharp
var pageData = data.Skip(10).Take(10).ToList(); // Sorğu yalnız lazımi məlumatı gətirir
```

Bu yanaşma **verilənlər bazasında** yalnız ehtiyac duyulan məlumatı gətirir və yaddaş istifadəsini optimallaşdırır.

---

## 3. Səhifələmə Nümunəsi

Aşağıdakı kod **ikinci səhifədən 10 elementi** gətirir:

```csharp
int pageNumber = 2;
int pageSize = 10;

var pageData = data
    .Skip((pageNumber - 1) * pageSize)
    .Take(pageSize)
    .ToList();

foreach (var item in pageData)
{
    Console.WriteLine(item);
}
```

🔹 **Nəticə:** **2-ci səhifədən 10 element gətirilir.**

---

## 4. Performans Təkmilləşdirməsi

Böyük verilər üzərində işləyərkən **`AsQueryable()`** istifadə edərək **performansı artırmaq** mümkündür:

✅ **Məsələn:**

```csharp
var pageData = context.Customers
    .AsQueryable()
    .Skip(20)
    .Take(10)
    .ToList();
```

Bu sorğu **yalnız ehtiyac duyulan məlumatı gətirir** və **verilənlər bazasında optimallaşdırılmış şəkildə işləyir**.

---

## 5. Dinamik Səhifələmə Metodu

İstifadəçi girişinə görə səhifələməni **dinamik şəkildə** idarə etmək üçün metod yaratmaq mümkündür:

✅ **Məsələn:**

```csharp
public List<T> GetPagedData<T>(IQueryable<T> query, int pageNumber, int pageSize)
{
    return query
        .Skip((pageNumber - 1) * pageSize)
        .Take(pageSize)
        .ToList();
}

// Metodun istifadəsi
var customersPage = GetPagedData(context.Customers, 3, 15);
```

🔹 **Bu metod səhifə ölçüsünü və səhifə nömrəsini dinamik idarə etməyə imkan verir.**

---

## 6. Ümumi Səhifə Sayısının Hesablanması

Səhifələmə zamanı **ümumi səhifə sayını** hesablamaq üçün verilənlərin ümumi sayını istifadə etmək lazımdır:

✅ **Məsələn:**

```csharp
int totalItems = context.Customers.Count();
int pageSize = 10;
int totalPages = (int)Math.Ceiling((double)totalItems / pageSize);

Console.WriteLine($"Ümumi Səhifə Sayı: {totalPages}");
```

🔹 **Nəticə:** **Ümumi səhifə sayısı müəyyən edilir.**

---

## 7. İrəli və Geri Keçid

İstifadəçilərin **səhifələr arasında rahat hərəkət etməsi üçün** səhifə dəyişmə mexanizmi qurula bilər:

✅ **Məsələn:**

```csharp
int currentPage = 1; // Mövcud səhifə

// Növbəti səhifəyə keçid
var nextPageData = data
    .Skip(currentPage * pageSize)
    .Take(pageSize)
    .ToList();

// Əvvəlki səhifəyə keçid
currentPage--;

var previousPageData = data
    .Skip((currentPage - 1) * pageSize)
    .Take(pageSize)
    .ToList();
```

🔹 **Bu yanaşma istifadəçilərin rahat şəkildə səhifələr arasında gəzməsinə imkan verir.**

---

## 8. Səhifələmə ilə API Cavabı (Paged Result)

Bir **API cavabında səhifələmə məlumatlarını** qaytarmaq üçün xüsusi bir **`PagedResult<T>`** modeli yaratmaq olar:

✅ **Məsələn:**

```csharp
public class PagedResult<T>
{
    public List<T> Items { get; set; }
    public int TotalCount { get; set; }
    public int TotalPages { get; set; }
}

public PagedResult<T> GetPagedResult<T>(IQueryable<T> query, int pageNumber, int pageSize)
{
    int totalCount = query.Count();
    var items = query
        .Skip((pageNumber - 1) * pageSize)
        .Take(pageSize)
        .ToList();

    return new PagedResult<T>
    {
        Items = items,
        TotalCount = totalCount,
        TotalPages = (int)Math.Ceiling((double)totalCount / pageSize)
    };
}

// Metodun istifadəsi
var customersPage = GetPagedResult(context.Customers, 2, 10);
Console.WriteLine($"Ümumi Məlumat Sayı: {customersPage.TotalCount}");
Console.WriteLine($"Ümumi Səhifə Sayı: {customersPage.TotalPages}");
```

🔹 **Bu yanaşma API-lərdə səhifələnmiş nəticələrin asanlıqla qaytarılmasını təmin edir.**

---

## **Nəticə**

| **Metod**         | **İstifadə Halı**                                             |
|-------------------|--------------------------------------------------------------|
| **Take(n)**       | İlk `n` elementin götürülməsi üçün.                          |
| **Skip(n)**       | İlk `n` elementi ötürərək qalanları gətirmək üçün.           |
| **Skip().Take()** | Məlumatları səhifələməklə **performanslı** şəkildə almaq üçün. |
