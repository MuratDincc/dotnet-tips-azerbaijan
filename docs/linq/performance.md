# Performans Optimizasiyası  

Performans optimizasiyası, xüsusilə böyük miqyaslı tətbiqlərdə resurs istehlakını azaltmaq və istifadəçi təcrübəsini yaxşılaşdırmaq üçün həyati əhəmiyyət daşıyır. Yanlış tətbiqlər sistemi yavaşlada, hətta çökməsinə səbəb ola bilər.  

---

## 1. Lazımsız Məlumat Çıxarmaq  

❌ **Yanlış İstifadə:** Lazımsız bütün sütunları sorğuya daxil etmək.  

```csharp
var products = context.Products.ToList(); // Bütün sütunlar gətirilir
```  

✅ **Düzgün İstifadə:** Yalnız lazım olan sütunları çıxarın.  

```csharp
var productNames = context.Products
    .Select(p => new { p.Name, p.Price })
    .ToList();
```  

---

## 2. Eager Loading və Lazy Loading-i Yanlış İdarə Etmək  

❌ **Yanlış İstifadə:** Lazımsız `Include` istifadəsi ilə həddindən artıq məlumat yükləmək.  

```csharp
var orders = context.Orders
    .Include(o => o.Customer)
    .Include(o => o.Products)
    .ToList();
```  

✅ **Düzgün İstifadə:** Yalnız ehtiyac duyulan məlumatlar üçün `Include` istifadə edin.  

```csharp
var orders = context.Orders
    .Include(o => o.Products)
    .Where(o => o.OrderDate > DateTime.UtcNow.AddMonths(-1))
    .ToList();
```  

---

## 3. N+1 Sorğu Problemini Nəzərə Almamaq  

❌ **Yanlış İstifadə:** Əlaqəli məlumatları ayrı-ayrı sorğularla gətirmək.  

```csharp
var customers = context.Customers.ToList();
foreach (var customer in customers)
{
    var orders = context.Orders.Where(o => o.CustomerId == customer.Id).ToList();
}
```  

✅ **Düzgün İstifadə:** Əlaqəli məlumatları tək sorğuda gətirin.  

```csharp
var customersWithOrders = context.Customers
    .Include(c => c.Orders)
    .ToList();
```  

---

## 4. Filtrləməni Yaddaşda Aparmaq  

❌ **Yanlış İstifadə:** Bütün məlumatları yaddaşda saxlayıb sonra filtrləmək.  

```csharp
var orders = context.Orders.ToList();
var recentOrders = orders.Where(o => o.OrderDate > DateTime.UtcNow.AddMonths(-1));
```  

✅ **Düzgün İstifadə:** Filtrləməni verilənlər bazasında aparın.  

```csharp
var recentOrders = context.Orders
    .Where(o => o.OrderDate > DateTime.UtcNow.AddMonths(-1))
    .ToList();
```  

---

## 5. İndeksləri Nəzərə Almamaq  

❌ **Yanlış İstifadə:** Sorğular üçün uyğun indekslərin olmaması.  

```sql
SELECT * FROM Orders WHERE CustomerId = 123;
-- Bu sorğu indeks yoxdursa yavaş işləyə bilər.
```  

✅ **Düzgün İstifadə:** Tez-tez istifadə olunan sütunlar üçün indekslər yaradın.  

```sql
CREATE INDEX IX_Orders_CustomerId ON Orders (CustomerId);
```  

---

## 6. Həddindən Artıq İzləmə (Tracking)  

❌ **Yanlış İstifadə:** Lazımsız izləmə (tracking) ilə resurs istehlakını artırmaq.  

```csharp
var products = context.Products.ToList(); // Tracking aktivdir
products[0].Price = 10; // Dəyişikliklər izlənilir
```  

✅ **Düzgün İstifadə:** Sorğuları izləməsiz icra edin.  

```csharp
var products = context.Products.AsNoTracking().ToList();
```  

---

## 7. Transaction İstifadəsini İhmal Etmək  

❌ **Yanlış İstifadə:** Çoxlu verilənlər bazası əməliyyatlarını transaction olmadan icra etmək.  

```csharp
context.Products.Add(new Product { Name = "Product1" });
context.SaveChanges();
context.Orders.Add(new Order { ProductId = 1, Quantity = 10 });
context.SaveChanges();
```  

✅ **Düzgün İstifadə:** Bütün əməliyyatları bir transaction daxilində icra edin.  

```csharp
using var transaction = context.Database.BeginTransaction();
context.Products.Add(new Product { Name = "Product1" });
context.SaveChanges();
context.Orders.Add(new Order { ProductId = 1, Quantity = 10 });
context.SaveChanges();
transaction.Commit();
```  