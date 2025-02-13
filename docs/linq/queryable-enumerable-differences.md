# LINQ: `IQueryable` və `IEnumerable` Arasındakı Fərqlər  

LINQ ilə işləyərkən `IQueryable` və `IEnumerable` interfeysləri arasında düzgün seçim etmək, performans və sorğu davranışı baxımından kritik əhəmiyyət daşıyır. Bu iki interfeysin işləmə prinsipləri və istifadə sahələri fərqlidir.  

---

## 1. IQueryable Nədir?  

- **Tərif:** Sorğunun verilənlər bazasında və ya uzaq bir mənbədə icra olunmasına imkan yaradır.  
- **İşləmə Prinsipi:** Sorğular verilənlər bazasına göndərilir və orada işlənir (deferred execution - gecikmiş icra).  

**Nümunə:**  

```csharp
using var context = new AppDbContext();

IQueryable<Customer> query = context.Customers.Where(c => c.IsActive);
var activeCustomers = query.ToList(); // Sorğu burada icra olunur
```

`IQueryable` sayəsində sorğu verilənlər bazasında icra olunur və yalnız ehtiyac duyulan məlumatlar gətirilir.  

---

## 2. IEnumerable Nədir?  

- **Tərif:** Məlumatı yaddaşa yükləyir və orada emal edir.  
- **İşləmə Prinsipi:** Sorğular yaddaşda icra olunur.  

**Nümunə:**  

```csharp
IEnumerable<Customer> customers = context.Customers.ToList();
var activeCustomers = customers.Where(c => c.IsActive).ToList(); // Filtrləmə yaddaşda aparılır
```

`IEnumerable`, bütün məlumatı yaddaşa yükləyir və filtrləmə prosesi yaddaşda həyata keçirilir.  

---

## 3. Yanlış və Düzgün İstifadə  

### **Yanlış İstifadə:** Böyük veriləri yaddaşa yükləmək  

❌ **Yanlış İstifadə:**  

```csharp
var allCustomers = context.Customers.ToList(); // Bütün məlumatı yaddaşa yükləyir
var activeCustomers = allCustomers.Where(c => c.IsActive).ToList();
```

Bu yanaşma, böyük verilər üçün lazımsız yaddaş istehlakına səbəb olur.  

---

### **Düzgün İstifadə:** Sorğuları verilənlər bazasında icra etmək  

✅ **Düzgün İstifadə:**  

```csharp
var activeCustomers = context.Customers
    .Where(c => c.IsActive)
    .ToList(); // Sorğu birbaşa verilənlər bazasında icra olunur
```

Bu üsul yalnız lazımi məlumatı gətirərək yaddaş istehlakını optimallaşdırır.  

---

## 4. `IQueryable` və `IEnumerable` Fərqləri  

| **Xüsusiyyət**             | **IQueryable**                         | **IEnumerable**                        |
|----------------------------|----------------------------------------|----------------------------------------|
| **İcra Yeri**              | Verilənlər bazası və ya uzaq mənbə     | Yaddaş                                 |
| **Performans**             | Daha yüksək (sorğu mənbədə icra edilir) | Daha aşağı (məlumat yaddaşda emal edilir) |
| **İstifadə Sahəsi**        | Böyük verilər, verilənlər bazası sorğuları | Kiçik verilər, yaddaş emalı           |
| **Lazy Execution**         | Bəli                                   | Bəli                                   |
| **Sorğu İşləmə**           | SQL kimi sorğu dilləri                 | Yaddaş üzərində LINQ                   |

---

## 5. Dinamik Sorğu Nümunəsi  

✅ **Nümunə:** İstifadəçi seçiminə əsasən sorğu  

```csharp
public List<Customer> GetCustomers(bool onlyActive)
{
    using var context = new AppDbContext();

    IQueryable<Customer> query = context.Customers;

    if (onlyActive)
    {
        query = query.Where(c => c.IsActive);
    }

    return query.ToList();
}
```

Bu üsul, yalnız ehtiyac duyulan məlumatları gətirərək sorğuların optimallaşdırılmasını təmin edir.  

---

## 6. Performans Testi  

`IQueryable` və `IEnumerable` arasındakı performans fərqini test etmək üçün aşağıdakı koddan istifadə edə bilərsiniz:  

✅ **Performans Testi:**  

```csharp
var stopwatch = Stopwatch.StartNew();

// IQueryable ilə
var activeCustomersQuery = context.Customers.Where(c => c.IsActive).ToList();
stopwatch.Stop();
Console.WriteLine($"IQueryable Müddəti: {stopwatch.ElapsedMilliseconds} ms");

stopwatch.Restart();

// IEnumerable ilə
var allCustomers = context.Customers.ToList();
var activeCustomersEnum = allCustomers.Where(c => c.IsActive).ToList();
stopwatch.Stop();
Console.WriteLine($"IEnumerable Müddəti: {stopwatch.ElapsedMilliseconds} ms");
```

---

## 7. Hansı Halda Hansı İstifadə Edilməlidir?  

| **Vəziyyət**                            | **Tercih Edilən İnterfeys** |
|-----------------------------------------|-----------------------------|
| Verilənlər bazası əməliyyatları         | IQueryable                  |
| Kiçik verilərlə yaddaşda işləmək        | IEnumerable                  |
| Dinamik sorğu yaratmaq                  | IQueryable                   |
| Böyük verilər üzərində performans kritikdir | IQueryable                  |
