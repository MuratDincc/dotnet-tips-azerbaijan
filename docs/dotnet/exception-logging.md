# Exception Qeydiyyatı

Exception qeydiyyatı, bir tətbiqdə meydana gələn xətaları anlamaq və düzəltmək üçün həyati əhəmiyyət daşıyır. Lakin, pis qurulmuş və ya yetərsiz logging strategiyaları, xəta ayırd etməyi çətinləşdirə bilər.

---

## 1. Exceptionların Loggingə Qeyd Edilməməsi

❌ **Yanlış İstifadə:** Exceptionları yalnız konsola yazdırmaq.

```csharp
try
{
    // Əməliyyat
}
catch (Exception ex)
{
    Console.WriteLine($"Exception: {ex.Message}");
}
```

✅ **Düzgün İstifadə:** Logging frameworkləri ilə Exceptionları ətraflı şəkildə qeyd edin.

```csharp
var logger = LoggerFactory.Create(builder => builder.AddConsole()).CreateLogger("AppLogger");

try
{
    // Əməliyyat
}
catch (Exception ex)
{
    logger.LogError(ex, "Bir Exception baş verdi.");
}
```

---

## 2. Yetərsiz Exception Mesajları

❌ **Yanlış İstifadə:** Exceptionların detalını (context) bildirməmək.

```csharp
catch (Exception ex)
{
    logger.LogError(ex.Message); // Bağlam əksik
}
```

✅ **Düzgün İstifadə:** Exception detalını açıq şəkildə bildirərək daha çox məlumat təmin edin.

```csharp
catch (Exception ex)
{
    logger.LogError(ex, "Verilənlər bazası əməliyyatı zamanı bir Exception baş verdi.");
}
```

---

## 3. Global Exception Logging'i Atlatmaq

❌ **Yanlış İstifadə:** Global Exception idarəetməsini və logginge qeydetməyi nəzərə almamaq.

```csharp
app.MapGet("/", () => throw new Exception("Exception!")); // Global idarəetmə yox
```

✅ **Düzgün İstifadə:** Global exception handler ilə bütün Exceptionları yaxalayın.

```csharp
app.UseExceptionHandler("/error");

app.Map("/error", (HttpContext context) =>
{
    var exception = context.Features.Get<IExceptionHandlerFeature>()?.Error;
    logger.LogError(exception, "Global bir Exception yaxalandı.");
    return Results.Problem(detail: exception?.Message, statusCode: 500);
});
```

---

## 4. Exceptionların Təkrarlanaraq Qeyd Edilməsi

❌ **Yanlış İstifadə:** Eyni Exceptionı birdən çox dəfə qeyd etmək.

```csharp
catch (Exception ex)
{
    logger.LogError(ex, "Exception!");
    throw; // Yenidən qeyd edilir.
}
```

✅ **Düzgün İstifadə:** Exceptionları yalnız bir dəfə qeyd edin.

```csharp
catch (Exception ex) when (LogException(ex))
{
    throw;
}

static bool LogException(Exception ex)
{
    logger.LogError(ex, "Exception qeyd edildi.");
    return false;
}
```

---

## 5. Həssas Məlumatların Logginge Qeyd Edilməsi

❌ **Yanlış İstifadə:** İstifadəçi məlumatlarını və ya həssas məlumatları loqlamaq.

```csharp
catch (Exception ex)
{
    logger.LogError($"Exception: {ex.Message}, İstifadəçi: {user.Password}"); // Təhlükəsizlik zəifliyi
}
```

✅ **Düzgün İstifadə:** Həssas məlumatları kənarlaşdırın.

```csharp
catch (Exception ex)
{
    logger.LogError(ex, "Exception baş verdi.");
}
```

---

## 6. Logging Səviyyələrini Yanlış İstifadə Etmək

❌ **Yanlış İstifadə:** Bütün Exceptionları eyni səviyyədə qeyd etmək.

```csharp
logger.LogError("Exception!"); // Hər şey Error olaraq loqlanıb.
```

✅ **Düzgün İstifadə:** Doğru log səviyyələrini istifadə edin.

```csharp
logger.LogInformation("Məlumat: Əməliyyat başladı.");
logger.LogWarning("Xəbərdarlıq: Gözlənilməz bir vəziyyət.");
logger.LogError("Exception: Bir istisna əmələ gəldi.");
```

---

## 7. Log İdarəetmə Sistemlərini İstifadə Etməmək

❌ **Yanlış İstifadə:** Lokal loqlama ilə məhdud qalmaq.

```csharp
logger.LogError("Exception qeyd edildi.");
```

✅ **Düzgün İstifadə:** Mərkəzi log idarəetmə alətlərini istifadə edin.

- **Azure Application Insights**
- **Elastic Stack (ELK)**
- **Sentry**
- **Loggly**
- **Seq**
- **Splunk**
- **Datadog**
- **Raygun**
- **New Relic**
- **Serilog**
- **NLog**
- **Log4Net**
- **Graylog**
