# Exception Handling

Exception handling (error handling), proqram təminatının etibarlılığını və sabitliyini artıran vacib bir elementdir. Lakin, Exceptionların düzgün idarə olunmaması, Exceptionların aradan qaldırılmasını çətinləşdirə və tətbiqin performansına mənfi təsir göstərə bilər.

---

## 1. Exceptionların Ümumi Tutulması

❌ **Yanlış İstifadə:** Bütün Exceptionları `Exception` sinifi ilə tutmaq.

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

✅ **Düzgün İstifadə:** Spesifik Exception növlərini tutaraq daha ətraflı Exception idarəetməsi həyata keçirin.

```csharp
try
{
    // Əməliyyat
}
catch (NullReferenceException ex)
{
    Console.WriteLine($"Null referans Exceptionı: {ex.Message}");
}
catch (FileNotFoundException ex)
{
    Console.WriteLine($"Fayl tapılmadı: {ex.Message}");
}
catch (Exception ex)
{
    Console.WriteLine($"Gözlənilməz bir Exception: {ex.Message}");
}
```

---

## 2. Finally Blokunun Yanlış İstifadəsi

❌ **Yanlış İstifadə:** `finally` bloku içində kodun nəzarətdən çıxarılması.

```csharp
try
{
    // Əməliyyat
}
finally
{
    throw new InvalidOperationException("Səhv əməliyyat");
}
```

✅ **Düzgün İstifadə:** Resursların düzgün şəkildə sərbəst buraxılmasını təmin edin.

```csharp
FileStream? file = null;

try
{
    file = new FileStream("data.txt", FileMode.Open);
    // Əməliyyat
}
finally
{
    file?.Dispose();
}
```

---

## 3. Asinxron Exception İdarəetməsi

❌ **Yanlış İstifadə:** Asinxron Exceptionları `await` etmədən tutmağa çalışmaq.

```csharp
try
{
    DoAsync(); // await əskik
}
catch (Exception ex)
{
    Console.WriteLine($"Exception: {ex.Message}");
}
```

✅ **Düzgün İstifadə:** Asinxron əməliyyatları `await` ilə tutaraq düzgün Exception idarəetməsini təmin edin.

```csharp
try
{
    await DoAsync();
}
catch (Exception ex)
{
    Console.WriteLine($"Asinxron Exception: {ex.Message}");
}
```

---

## 4. Exceptionların Loggingdə Qeyd Edilməməsi

❌ **Yanlış İstifadə:** Exceptionları yalnız konsola yazmaq.

```csharp
catch (Exception ex)
{
    Console.WriteLine($"Exception: {ex.Message}");
}
```

✅ **Düzgün İstifadə:** Logging frameworkləri ilə Exceptionları qeyd edin.

```csharp
var logger = LoggerFactory.Create(builder => builder.AddConsole()).CreateLogger("ErrorLogger");

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

## 5. Global Exception Handler

❌ **Yanlış İstifadə:** Bütün Exceptionları qlobal olaraq idarə etməmək.

```csharp
app.MapGet("/", () => throw new Exception("Bir Exception baş verdi!"));
```

✅ **Düzgün İstifadə:** Bütün tətbiq boyunca Exceptionları idarə etmək üçün bir middleware istifadə edin.

```csharp
app.UseExceptionHandler("/error");

app.Map("/error", (HttpContext context) =>
{
    var exception = context.Features.Get<IExceptionHandlerFeature>()?.Error;
    return Results.Problem(detail: exception?.Message, statusCode: 500);
});
```

---

## 6. Exception İdarəetməsində Xüsusi Siniflərin İstifadəsi

❌ **Yanlış İstifadə:** Exception idarəetməsi üçün standart `Exception` sinifini birbaşa istifadə etmək.

```csharp
throw new Exception("Səhv əməliyyat");
```

✅ **Düzgün İstifadə:** Xüsusi Exception sinifləri yaradaraq daha mənalı Exception mesajları təmin edin.

```csharp
public class CustomException : Exception
{
    public CustomException(string message) : base(message) { }
}

throw new CustomException("Bu xüsusi bir Exceptiondır.");
```

---

## 7. Exceptionların Səssizcə Udulması

❌ **Yanlış İstifadə:** Exceptionları tutub heç bir əməliyyat etməmək.

```csharp
try
{
    // Əməliyyat
}
catch
{
    // Səssizcə udulan Exception
}
```

✅ **Düzgün İstifadə:** Exceptionları düzgün bir şəkildə emal edin və ya qeyd edin.

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
