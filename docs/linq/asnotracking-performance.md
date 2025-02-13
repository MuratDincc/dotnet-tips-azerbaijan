# AsNoTracking İstifadəsi ilə Performans Artırımı

Entity Framework, verilənlər bazası sorğularını izləmək və dəyişiklikləri nəzarətdə saxlamaq üçün standart olaraq bir **tracking** mexanizmi istifadə edir. Lakin, yalnız oxuma əməliyyatları üçün bu izləmə mexanizmi lazımsızdır və performans itkisinə səbəb ola bilər. `AsNoTracking` metodu bu problemi həll edərək izləmə mexanizmini deaktiv edir və performansı artırır.

---

## 1. AsNoTracking Nədir?

`AsNoTracking`, sorğudan gələn obyektlərin dəyişikliklərinin izlənilməsini deaktiv edən bir Entity Framework xüsusiyyətidir. Xüsusilə yalnız oxuma məqsədli sorğularda yaddaş və prosessor istifadəsini azaldaraq daha səmərəli işləmə imkanı yaradır.

---

## 2. Səhv və Düzgün İstifadə

### **Səhv İstifadə:** Tracking aktiv olduqda lazımsız yaddaş istifadəsi.

❌ **Səhv İstifadə:**

```csharp
using var context = new AppDbContext();

var customers = await context.Customers
    .Where(c => c.IsActive)
    .ToListAsync();

foreach (var customer in customers)
{
    Console.WriteLine(customer.Name);
}
```

Bu sorğu, `customers` siyahısındakı hər bir obyekt üçün izləmə məlumatı saxlayır. Ancaq yalnız oxuma məqsədilə işlədikdə, bu izləmə lazımsızdır və əlavə resurs istifadəsinə səbəb olur.

---

### **Düzgün İstifadə:** `AsNoTracking` ilə lazımsız izləməni deaktiv etmək.

✅ **Düzgün İstifadə:**

```csharp
using var context = new AppDbContext();

var customers = await context.Customers
    .AsNoTracking()
    .Where(c => c.IsActive)
    .ToListAsync();

foreach (var customer in customers)
{
    Console.WriteLine(customer.Name);
}
```

Bu üsul yalnız oxuma əməliyyatlarında istifadə olunduğundan izləmə mexanizmi deaktiv edilir və performans artır.

---

## 3. Performans Qazancları

`AsNoTracking`, xüsusilə aşağıdakı hallarda performansı artırır:
- Böyük verilənlər bazası cədvəllərində sorğular icra edilərkən.
- Sorğular yalnız məlumat oxumaq üçün istifadə edilərkən.
- Birdən çox sorğu eyni anda icra edilərkən.

Böyük miqyaslı sistemlərdə bu fərq əhəmiyyətli dərəcədə hiss olunur.

---

## 4. `AsNoTrackingWithIdentityResolution` İstifadəsi

`AsNoTracking` metodu ilə eyni identifikatora sahib obyektlərin düzgün idarə olunması üçün `AsNoTrackingWithIdentityResolution` istifadə edilə bilər.

✅ **Nümunə:**

```csharp
using var context = new AppDbContext();

var orders = await context.Orders
    .AsNoTrackingWithIdentityResolution()
    .Include(o => o.Customer)
    .ToListAsync();

foreach (var order in orders)
{
    Console.WriteLine($"Order ID: {order.Id}, Customer: {order.Customer.Name}");
}
```

Bu üsul, əlaqəli məlumatlarla işləyərkən izləmə mexanizmi olmadan eyni ID-yə sahib obyektləri birləşdirir.

---

## 5. Performans Testi

Aşağıdakı nümunə `AsNoTracking` ilə adi sorğular arasındakı performans fərqini göstərir:

```csharp
var stopwatch = Stopwatch.StartNew();

using var context = new AppDbContext();

// AsNoTracking olmadan
var trackedCustomers = await context.Customers
    .Where(c => c.IsActive)
    .ToListAsync();

stopwatch.Stop();
Console.WriteLine($"Tracked Query Time: {stopwatch.ElapsedMilliseconds} ms");

stopwatch.Restart();

// AsNoTracking ilə
var untrackedCustomers = await context.Customers
    .AsNoTracking()
    .Where(c => c.IsActive)
    .ToListAsync();

stopwatch.Stop();
Console.WriteLine($"Untracked Query Time: {stopwatch.ElapsedMilliseconds} ms");
```

Bu test nəticələrində `AsNoTracking` istifadəsi ilə daha sürətli sorğular və daha az yaddaş istifadəsi müşahidə olunacaq.