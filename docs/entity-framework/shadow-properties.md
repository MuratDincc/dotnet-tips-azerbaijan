# Shadow Properties

Entity Framework Core-da "Shadow Properties", bir entity üzərində təyin olunmayan, lakin verilənlər bazasında saxlanılan xüsusiyyətlərdir. Bu xüsusiyyətlər, xüsusilə genişləndirilə bilmə qabiliyyəti və elastiklik təmin etsə də, yanlış istifadələri verilənlər uyğunsuzluğuna və kod mürəkkəbliyinə yol aça bilər.

---

## 1. Shadow Propertiesni Gərəksiz İstifadə Etmək

❌ **Yanlış İstifadə:** entity sinifində təyin oluna biləcək bir xüsusiyyəti kölgə xüsusiyyəti olaraq istifadə etmək.

```csharp
modelBuilder.Entity<Product>()
    .Property<DateTime>("LastUpdated");
```

✅ **Düzgün İstifadə:** Gərəksiz shadow properties istifadəsindən qaçının və entity sinifinə əlavə edin.

```csharp
public class Product
{
    public int Id { get; set; }
    public DateTime LastUpdated { get; set; }
}
```

---

## 2. Shadow Propertiesnin Dəyərlərini Yanlış İdarə Etmək

❌ **Yanlış İstifadə:** Shadow properties dəyərini yoxlamadan istifadə etmək.

```csharp
var lastUpdated = context.Entry(product).Property("LastUpdated").CurrentValue;
Console.WriteLine(lastUpdated);
```

✅ **Düzgün İstifadə:** Shadow properties dəyərlərini təhlükəsiz bir şəkildə idarə edin.

```csharp
var lastUpdatedProperty = context.Entry(product).Property("LastUpdated");
if (lastUpdatedProperty != null)
{
    Console.WriteLine(lastUpdatedProperty.CurrentValue);
}
else
{
    Console.WriteLine("LastUpdated xüsusiyyəti mövcud deyil.");
}
```

---

## 3. Verilənlər Uyğunluğunu Göz Ardı Etmək

❌ **Yanlış İstifadə:** Shadow properties dəyərlərini güncəlləmədən buraxmaq.

```csharp
var product = context.Products.Find(1);
context.Entry(product).Property("LastUpdated").CurrentValue = null; // Dəyər tutarsızlığı yaradır
context.SaveChanges();
```

✅ **Düzgün İstifadə:** Shadow properties dəyərlərini hər əməliyyatda uyğun şəkildə güncəlləyin.

```csharp
var product = context.Products.Find(1);
context.Entry(product).Property("LastUpdated").CurrentValue = DateTime.UtcNow;
context.SaveChanges();
```

---

## 4. Shadow Propertiesni Səhv Ayırd Etmə (Debug) Prosesində Göz Ardı Etmək

❌ **Yanlış İstifadə:** Shadow Propertiesnin səhv ayırd etmə zamanı görünürlüyünü təmin etməmək.

```csharp
var product = context.Products.Find(1);
// Kölgə xüsusiyyəti dəyərlərini incələmədən keçmək
```

✅ **Düzgün İstifadə:** Səhv ayırd etmə zamanı Shadow Propertiesnin dəyərlərini yoxlayın.

```csharp
var product = context.Products.Find(1);
var lastUpdated = context.Entry(product).Property("LastUpdated").CurrentValue;
Console.WriteLine($"LastUpdated: {lastUpdated}");
```

---

## 5. Shadow Properties ilə Yanlış Əlaqələr Qurmaq

❌ **Yanlış İstifadə:** Shadow Propertiesni əlaqələrdə birbaşa istifadə etmək.

```csharp
modelBuilder.Entity<Order>()
    .HasOne<Product>()
    .WithMany()
    .HasForeignKey("ProductId");
```

✅ **Düzgün İstifadə:** Kölgə xüsusiyyəti yerinə açıq şəkildə təyin olunmuş əlaqələr istifadə edin.

```csharp
public class Order
{
    public int Id { get; set; }
    public int ProductId { get; set; }
    public Product Product { get; set; }
}
```
