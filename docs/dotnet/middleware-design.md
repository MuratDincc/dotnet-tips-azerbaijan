# Middleware Dizaynı və Tətbiqi

ASP.NET Core-da middleware, bir HTTP sorğusu və cavabı üzərində əməliyyat aparmaq üçün istifadə edilən bir proqram təminatı qatmanıdır. Middleware-in yanlış dizayn edilməsi və ya tətbiq edilməsi performans problemlərinə və mürəkkəb kod strukturuna yol aça bilər.

---

## 1. Gərəksiz Middleware İstifadəsi

❌ **Yanlış İstifadə:** Hər kiçik əməliyyat üçün ayrı middleware yaratmaq.

```csharp
public class LoggingMiddleware
{
    private readonly RequestDelegate _next;

    public LoggingMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    public async Task InvokeAsync(HttpContext context)
    {
        Console.WriteLine("Sorğu: " + context.Request.Path);
        await _next(context);
    }
}
```

✅ **Düzgün İstifadə:** Əlaqədar əməliyyatları tək bir middleware içində qruplaşdırın.

```csharp
public class CombinedMiddleware
{
    private readonly RequestDelegate _next;

    public CombinedMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    public async Task InvokeAsync(HttpContext context)
    {
        Console.WriteLine("Sorğu: " + context.Request.Path);

        if (context.Request.Path.StartsWithSegments("/secure"))
        {
            // Əlavə təhlükəsizlik yoxlaması
            if (!context.User.Identity.IsAuthenticated)
            {
                context.Response.StatusCode = 401;
                return;
            }
        }

        await _next(context);
    }
}
```

---

## 2. Middleware-in Yanlış Sıralanması

❌ **Yanlış İstifadə:** Middleware sırasının əhəmiyyətli olduğu hallarda yanlış sırayla əlavə etmək.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

✅ **Düzgün İstifadə:** Middleware sırasını doğru bir şəkildə qurun.

```csharp
app.UseAuthentication();
app.UseAuthorization();
app.MapControllers();
```

> 💡 **Qeyd:** Doğru middleware sırası, tətbiqin düzgün işləməsi üçün həyati əhəmiyyət daşıyır.

---

## 3. Response-ı Manual Olaraq Yazmaq

❌ **Yanlış İstifadə:** Cavabın hamısını manual olaraq idarə etmək.

```csharp
public async Task InvokeAsync(HttpContext context)
{
    if (context.Request.Path == "/custom")
    {
        context.Response.StatusCode = 200;
        await context.Response.WriteAsync("Xüsusi Cavab");
        return;
    }

    await _next(context);
}
```

✅ **Düzgün İstifadə:** `Results` sinifini və ya uyğun alətlərdən istifadə edərək cavabları idarə edin.

```csharp
public async Task InvokeAsync(HttpContext context)
{
    if (context.Request.Path == "/custom")
    {
        context.Response.StatusCode = StatusCodes.Status200OK;
        await Results.Text("Xüsusi Cavab").ExecuteAsync(context);
        return;
    }

    await _next(context);
}
```

---

## 4. Exception İdarəetməsini Nəzərə Almamaq

❌ **Yanlış İstifadə:** Middleware içində yaxalanmayan istisnalar buraxmaq.

```csharp
public async Task InvokeAsync(HttpContext context)
{
    // Səhvdir, çünki istisna idarəetməsi yoxdur
    var result = int.Parse("NotAnInt");
    await _next(context);
}
```

✅ **Düzgün İstifadə:** Exception idarəetməsini daxil edərək istisnaları yaxalayın.

```csharp
public async Task InvokeAsync(HttpContext context)
{
    try
    {
        var result = int.Parse("NotAnInt");
    }
    catch (Exception ex)
    {
        context.Response.StatusCode = StatusCodes.Status500InternalServerError;
        await context.Response.WriteAsync($"Səhv: {ex.Message}");
        return;
    }

    await _next(context);
}
```

---

## 5. Performans İtkilərinə Yol Açan Middleware

❌ **Yanlış İstifadə:** Gərəksiz loqlama etmək.

```csharp
public async Task InvokeAsync(HttpContext context)
{
    Console.WriteLine($"Sorğu Yolu: {context.Request.Path}");
    Console.WriteLine($"Başlıqlar: {string.Join(", ", context.Request.Headers.Select(h => h.Key + ": " + h.Value))}");
    await _next(context);
}
```

✅ **Düzgün İstifadə:** Performans baxımından kritik əməliyyatları minimuma endirin.

```csharp
public async Task InvokeAsync(HttpContext context)
{
    if (context.Request.Path.StartsWithSegments("/debug"))
    {
        Console.WriteLine($"Sorğu Yolu: {context.Request.Path}");
    }

    await _next(context);
}
```

---

## 6. Middleware-in Yenidən İstifadə Olunmaması

❌ **Yanlış İstifadə:** Middleware-i yalnız tək bir kontekstdə işləyəcək şəkildə dizayn etmək.

```csharp
app.UseMiddleware<MyCustomMiddleware>();
```

✅ **Düzgün İstifadə:** Middleware-i sürəli və yenidən istifadə oluna bilən hala gətirin.

```csharp
public class MyCustomMiddleware
{
    private readonly RequestDelegate _next;
    private readonly string _customMessage;

    public MyCustomMiddleware(RequestDelegate next, string customMessage)
    {
        _next = next;
        _customMessage = customMessage;
    }

    public async Task InvokeAsync(HttpContext context)
    {
        Console.WriteLine(_customMessage);
        await _next(context);
    }
}

app.UseMiddleware<MyCustomMiddleware>("Salam Dünya!");
```
