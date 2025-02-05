# Keyless Entitetlər

Entity Framework Core-da "Keyless Entitetlər", ilkin açara (primary key) ehtiyac duymayan və ümumiyyətlə yalnız oxumaq məqsədli istifadə olunan varlıqları təmsil edir. Bu xüsusiyyət, görünüşlər, ilkin açarsız cədvəllər və ya xüsusi SQL sorğularını xəritələmək kimi hallarda olduqca faydalıdır. Lakin, yanlış istifadəsi performans və verilənlər uyğunsuzluğu problemlərinə səbəb ola bilər.

---

## 1. Keyless Entitetlər İstifadəsini Yanlış Anlamaq

❌ **Yanlış İstifadə:** Keyless entitetləri dəyişiklik izləmə (change tracking) üçün istifadə etmək.

```csharp
[Keyless]
public class Report
{
    public int Id { get; set; } // Yanlış: Keyless entitetdə ilkin açar olmamalıdır.
    public string ReportName { get; set; }
}
```

✅ **Düzgün İstifadə:** Keyless entitetlər yalnız oxumaq məqsədli istifadə olunmalıdır.

```csharp
[Keyless]
public class Report
{
    public string ReportName { get; set; }
    public DateTime GeneratedOn { get; set; }
}
```

---

## 2. Gərəksiz Dəyişiklik İzləməsi Etmək

❌ **Yanlış İstifadə:** Keyless entitetlərlə məlumat güncəlləməyə çalışmaq.

```csharp
var report = new Report { ReportName = "İllik Hesabat" };
context.Reports.Add(report); // Səhv: Keyless entitetlər əlavə edilə bilməz
context.SaveChanges();
```

✅ **Düzgün İstifadə:** Keyless entitetlər yalnız sorğulama məqsədi ilə istifadə olunmalıdır.

```csharp
var reports = context.Reports
    .Where(r => r.GeneratedOn > DateTime.UtcNow.AddDays(-30))
    .ToList();
```

---

## 3. Keyless Entitetləri Defolt Şəkildə İstifadə Etmək

❌ **Yanlış İstifadə:** Keyless entitetləri təyin edərkən lazım olan konfiqurasiyaları etməmək.

```csharp
public class Report
{
    public string ReportName { get; set; }
    public DateTime GeneratedOn { get; set; }
}
```

✅ **Düzgün İstifadə:** `[Keyless]` və ya `.HasNoKey()` ilə açıq şəkildə konfiqurasiya edilməlidir.

```csharp
[Keyless]
public class Report
{
    public string ReportName { get; set; }
    public DateTime GeneratedOn { get; set; }
}
```

Və ya Fluent API istifadə edərək:

```csharp
modelBuilder.Entity<Report>().HasNoKey();
```

---

## 4. Performans Optimizasiyasını Göz Ardı Etmək

❌ **Yanlış İstifadə:** Böyük verilən dəstlərini sorğulayarkən Keyless entitetlər üçün optimallaşdırılmamış sorğular yazmaq.

```csharp
var reports = context.Reports.ToList(); // Bütün verilən dəstini yüklər
```

✅ **Düzgün İstifadə:** Sorğuları filtrləyərək optimallaşdırın.

```csharp
var recentReports = context.Reports
    .Where(r => r.GeneratedOn > DateTime.UtcNow.AddMonths(-1))
    .ToList();
```

---

## 5. Verilənlər Uyğunluğunu Göz Ardı Etmək

❌ **Yanlış İstifadə:** Keyless entitetləri əlaqəli verilənlərlə səhv bir şəkildə istifadə etmək.

```csharp
public class OrderSummary
{
    public int OrderId { get; set; }
    public decimal Total { get; set; }
    public string CustomerName { get; set; }
}
```

✅ **Düzgün İstifadə:** Keyless entitetlərdəki verilənlər yalnız oxunaqlı olmalıdır.

```csharp
[Keyless]
public class OrderSummary
{
    public decimal Total { get; set; }
    public string CustomerName { get; set; }
}
```
