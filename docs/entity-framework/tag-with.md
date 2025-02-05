# TagWith

Entity Framework Core-da `TagWith`, SQL sorğularına şərhlər əlavə etmək üçün istifadə olunan bir xüsusiyyətdir. Bu şərhlər, sorğu performansı və səhv ayırd etmə proseslərində əhəmiyyətli bir köməkçidir. Lakin, yanlış istifadəsi sorğu mürəkkəbliyini artıra və gərəksiz resurs istehlakına səbəb ola bilər.

---

## 1. TagWith'i Gərəksiz İstifadə Etmək

❌ **Yanlış İstifadə:** Hər sorğuya gərəksiz izahatlar əlavə etmək.

```csharp
var users = context.Users
    .TagWith("Bütün istifadəçiləri çəkir")
    .ToList();
```

✅ **Düzgün İstifadə:** Yalnız performans analizi və səhv ayırd etmə üçün kritik sorğulara izahat əlavə edin.

```csharp
var activeUsers = context.Users
    .Where(u => u.IsActive)
    .TagWith("Aylıq hesabat üçün aktiv istifadəçiləri çəkir")
    .ToList();
```

---

## 2. İzahatları Yetərsiz və ya Mənasız Etmək

❌ **Yanlış İstifadə:** İzahatları qısa və yetərsiz buraxmaq.

```csharp
var orders = context.Orders
    .TagWith("Sifariş sorğusu")
    .ToList();
```

✅ **Düzgün İstifadə:** İzahatlara sorğunun məqsədi və bağlamı haqqında məlumat əlavə edin.

```csharp
var recentOrders = context.Orders
    .Where(o => o.OrderDate > DateTime.UtcNow.AddDays(-30))
    .TagWith("Satış hesabatı üçün son 30 gündə yerləşdirilən sifarişləri çəkir")
    .ToList();
```

---

## 3. Birdən Çox TagWith İstifadə Etmək

❌ **Yanlış İstifadə:** Eyni sorğuda birdən çox `TagWith` çağırışı etmək.

```csharp
var data = context.Products
    .TagWith("Məhsulları çəkir")
    .TagWith("İnventar hesabatı üçün")
    .ToList();
```

✅ **Düzgün İstifadə:** Tək bir `TagWith` çağırışında izahatları birləşdirin.

```csharp
var data = context.Products
    .TagWith("İnventar hesabatı üçün məhsulları çəkir")
    .ToList();
```

---

## 4. Səhv Ayırd Etmə Prosesini Göz Ardı Etmək

❌ **Yanlış İstifadə:** Səhv ayırd etmə zamanı `TagWith` xüsusiyyətini istifadə etməmək.

```csharp
var query = context.Customers
    .Where(c => c.IsActive)
    .ToList();
```

✅ **Düzgün İstifadə:** Səhv ayırd etmə zamanı sorğulara izahat əlavə edərək logları daha mənalı hala gətirin.

```csharp
var query = context.Customers
    .Where(c => c.IsActive)
    .TagWith("Səhv ayırd etmə üçün aktiv müştəriləri çəkir")
    .ToList();
```

---

## 5. İzahatlarda Dinamik Yazı İstifadə Etmək

❌ **Yanlış İstifadə:** İzahatlarda dinamik yazı istifadə edərək sorğu performansını mənfi təsir etmək.

```csharp
var productId = 123;
var product = context.Products
    .Where(p => p.Id == productId)
    .TagWith($"ID: {productId} olan məhsulu çəkir")
    .FirstOrDefault();
```

✅ **Düzgün İstifadə:** Dinamik verilənləri izahatlarda istifadə etməkdən qaçının.

```csharp
var product = context.Products
    .Where(p => p.Id == 123)
    .TagWith("ID ilə xüsusi məhsulu çəkir")
    .FirstOrDefault();
```