# Sağlamlıq Yoxlamaları və Tətbiq İzləməsi

Sağlamlıq yoxlamaları, bir tətbiqin və ya servislərin vəziyyətini yoxlamaq və problemləri tez bir zamanda aşkar etmək üçün istifadə olunur. Yanlış tətbiqlər sağlamlıq yoxlamalarının etibarlılığını azalda bilər və problemlərin gec fərq edilməsinə yol aça bilər.

---

## 1. Sağlamlıq Yoxlama Endpointlərinin Təhlükəsiz Qurulmaması

❌ **Yanlış İstifadə:** Sağlamlıq yoxlama endpointlərini hamıya açıq etmək.

```csharp
app.MapHealthChecks("/health");
```

✅ **Düzgün İstifadə:** Sağlamlıq yoxlama endpointlərini icazələndirmə ilə qoruyun.

```csharp
app.MapHealthChecks("/health").RequireAuthorization();
```

---

## 2. Sadə Cavablardan İstifadə Etmək

❌ **Yanlış İstifadə:** Sağlamlıq yoxlamaları üçün yetərsiz cavablar.

```plaintext
Sağlam
```

✅ **Düzgün İstifadə:** Ətraflı cavablar təmin edərək problemləri anlamağı asanlaşdırın.

```csharp
app.MapHealthChecks("/health", new HealthCheckOptions
{
    ResponseWriter = async (context, report) =>
    {
        var result = JsonSerializer.Serialize(new
        {
            status = report.Status.ToString(),
            checks = report.Entries.Select(entry => new
            {
                name = entry.Key,
                status = entry.Value.Status.ToString(),
                description = entry.Value.Description
            })
        });
        context.Response.ContentType = "application/json";
        await context.Response.WriteAsync(result);
    }
});
```

---

## 3. Yetərsiz Testlər

❌ **Yanlış İstifadə:** Yalnız əsas yoxlamalar etmək.

```csharp
services.AddHealthChecks();
```

✅ **Düzgün İstifadə:** Lazım olan sistem komponentlərini yoxlayın.

```csharp
services.AddHealthChecks()
    .AddSqlServer("Server=localhost;Database=MyDb;User=sa;Password=Your_password123;")
    .AddRedis("localhost:6379")
    .AddCheck("Xüsusi Yoxlama", () =>
        HealthCheckResult.Healthy("Xüsusi nəzarət uğurlu!"));
```

---

## 4. Sağlamlıq Yoxlamalarının Daimi İzlənilməməsi

❌ **Yanlış İstifadə:** Sağlamlıq yoxlama nəticələrini yalnız manual olaraq yoxlamaq.

```plaintext
// Avtomatik izləmə edilmir
```

✅ **Düzgün İstifadə:** Sağlamlıq yoxlama nəticələrini izləmə alətləri ilə inteqrasiya edin.

- **Nümunə:** Prometheus və Grafana, Azure Monitor, Datadog

```csharp
services.AddHealthChecks()
    .AddCheck("Xüsusi Yoxlama", () =>
        HealthCheckResult.Healthy("Hər şey qaydasındadır!"))
    .ForwardToPrometheus();
```

---

## 5. Timeout Müddətlərini Yanlış Təyin Etmək

❌ **Yanlış İstifadə:** Çox qısa və ya uzun timeout müddətləri istifadə etmək.

```csharp
services.AddHealthChecks().AddSqlServer("Connection String", timeout: TimeSpan.FromSeconds(1));
```

✅ **Düzgün İstifadə:** Uyğun timeout müddətləri təyin edin.

```csharp
services.AddHealthChecks().AddSqlServer("Connection String", timeout: TimeSpan.FromSeconds(5));
```

---

## 6. Sağlamlıq Yoxlama Məlumatlarını Loqlamamaq

❌ **Yanlış İstifadə:** Sağlamlıq yoxlama nəticələrini loqlamamaq.

✅ **Düzgün İstifadə:** Sağlamlıq yoxlaması nəticələrini loqlayaraq izlənilə bilməsini artırın.

```csharp
app.MapHealthChecks("/health", new HealthCheckOptions
{
    ResponseWriter = async (context, report) =>
    {
        foreach (var entry in report.Entries)
        {
            logger.LogInformation("Sağlamlıq Yoxlaması: {Name}, Status: {Status}",
                entry.Key, entry.Value.Status);
        }
        await context.Response.WriteAsync("Sağlamlıq yoxlaması tamamlandı.");
    }
});
```

---

## 7. Xüsusi Sağlamlıq Yoxlamalarının Pis Dizayn Edilməsi

❌ **Yanlış İstifadə:** Xüsusi sağlamlıq yoxlamalarında mənalı olmayan vəziyyətlər qaytarmaq.

```csharp
public class CustomHealthCheck : IHealthCheck
{
    public Task<HealthCheckResult> CheckHealthAsync(HealthCheckContext context, CancellationToken cancellationToken = default)
    {
        return Task.FromResult(HealthCheckResult.Unhealthy());
    }
}
```

✅ **Düzgün İstifadə:** Xüsusi sağlamlıq yoxlamalarını sistem vəziyyəti haqqında doğru məlumat verəcək şəkildə dizayn edin.

```csharp
public class CustomHealthCheck : IHealthCheck
{
    public Task<HealthCheckResult> CheckHealthAsync(HealthCheckContext context, CancellationToken cancellationToken = default)
    {
        var isHealthy = CheckSomeCriticalResource();
        return Task.FromResult(isHealthy
            ? HealthCheckResult.Healthy("Sistem işləyir.")
            : HealthCheckResult.Unhealthy("Sistem kritik bir mənbə ilə əlaqə qura bilmir."));
    }

    private bool CheckSomeCriticalResource()
    {
        // Kritik mənbənin yoxlanılması
        return true;
    }
}
```
