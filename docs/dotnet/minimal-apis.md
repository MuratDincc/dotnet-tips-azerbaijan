# Minimal API-lər

Minimal API-lər, ASP.NET Core ilə sürətli və sadə bir şəkildə API yaratmaq üçün təqdim olunan bir xüsusiyyətdir. Lakin, düzgün şəkildə istifadə edilmədikdə, performans problemlərinə və kod mürəkkəbliyinə yol aça bilər.

---

## 1. Tək Məsuliyyət Prinsipi

❌ **Yanlış İstifadə:** Bütün iş məntiqini bir endpointə daxil etmək.

```csharp
app.MapGet("/users", async (HttpContext context) =>
{
    var users = await GetUsersFromDatabaseAsync();
    if (users == null)
    {
        context.Response.StatusCode = 404;
        await context.Response.WriteAsync("İstifadəçi tapılmadı.");
        return;
    }
    await context.Response.WriteAsJsonAsync(users);
});
```

✅ **Düzgün İstifadə:** İş məntiqini ayrı bir servisə daşıyaraq kodun oxunaqlılığını artırın.

```csharp
app.MapGet("/users", async (IUserService userService) =>
{
    var users = await userService.GetAllUsersAsync();
    return users != null ? Results.Ok(users) : Results.NotFound("İstifadəçi tapılmadı.");
});
```

---

## 2. Asılılıqların Birbaşa İdarə Edilməsi

❌ **Yanlış İstifadə:** Asılılıqların birbaşa nümunələrini yaratmaq.

```csharp
app.MapGet("/products", async () =>
{
    using var dbContext = new ProductDbContext();
    var products = await dbContext.Products.ToListAsync();
    return products;
});
```

✅ **Düzgün İstifadə:** Asılılıq injection istifadə edərək kodu test edilə bilən hala gətirin.

```csharp
app.MapGet("/products", async (IProductService productService) =>
{
    var products = await productService.GetAllProductsAsync();
    return Results.Ok(products);
});
```

---

## 3. Yanlış HTTP Status Kodları

❌ **Yanlış İstifadə:** Bütün statuslar üçün yalnız 200 (OK) qaytarmaq.

```csharp
app.MapGet("/orders/{id}", async (int id, IOrderService orderService) =>
{
    var order = await orderService.GetOrderByIdAsync(id);
    return order; // Səhvdir, çünki status kodları göstərilməyib.
});
```

✅ **Düzgün İstifadə:** HTTP status kodlarını açıq şəkildə göstərmək.

```csharp
app.MapGet("/orders/{id}", async (int id, IOrderService orderService) =>
{
    var order = await orderService.GetOrderByIdAsync(id);
    return order != null ? Results.Ok(order) : Results.NotFound("Sifariş tapılmadı.");
});
```

---

## 4. Middleware-in Yanlış İstifadəsi

🔴 **Yanlış İstifadə:** Middleware-i endpoint içində manual olaraq çağırmaq.

```csharp
app.MapGet("/middleware-test", async (HttpContext context) =>
{
    // Səhvdir, çünki middleware burada əllə idarə olunur.
    if (!context.Request.Headers.ContainsKey("Authorization"))
    {
        context.Response.StatusCode = 401;
        return;
    }
    await context.Response.WriteAsync("İcazə verildi.");
});
```

✅ **Düzgün İstifadə:** Middleware-i qlobal bir quruluş kimi təyin etmək.

```csharp
app.Use(async (context, next) =>
{
    if (!context.Request.Headers.ContainsKey("Authorization"))
    {
        context.Response.StatusCode = 401;
        return;
    }
    await next();
});

app.MapGet("/middleware-test", () => "İcazə verildi.");
```

---

## 5. Resurs İdarəetməsi

🔴 **Yanlış İstifadə:** Resursların düzgün şəkildə sərbəst buraxılmaması.

```csharp
app.MapGet("/files", async () =>
{
    var fileStream = new FileStream("data.txt", FileMode.Open);
    var content = await new StreamReader(fileStream).ReadToEndAsync();
    return content; // Səhvdir, çünki fayl bağlanmır.
});
```

✅ **Düzgün İstifadə:** Resurs idarəetməsini `using` ilə nəzarət edin.

```csharp
app.MapGet("/files", async () =>
{
    using var fileStream = new FileStream("data.txt", FileMode.Open);
    using var reader = new StreamReader(fileStream);
    var content = await reader.ReadToEndAsync();
    return Results.Ok(content);
});
```

---

## 6. Çox Sayda Endpoint Təyini

🔴 **Yanlış İstifadə:** Hər endpoint üçün oxşar məntiqin təkrarlanması.

```csharp
app.MapGet("/get-users", async (IUserService userService) => await userService.GetAllUsersAsync());
app.MapGet("/get-orders", async (IOrderService orderService) => await orderService.GetAllOrdersAsync());
app.MapGet("/get-products", async (IProductService productService) => await productService.GetAllProductsAsync());
```

✅ **Düzgün İstifadə:** Ümumi davranışları bir konfiqurasiya metodunda qruplaşdırın.

```csharp
void MapEndpoints(WebApplication app)
{
    app.MapGet("/users", async (IUserService userService) => await userService.GetAllUsersAsync());
    app.MapGet("/orders", async (IOrderService orderService) => await orderService.GetAllOrdersAsync());
    app.MapGet("/products", async (IProductService productService) => await productService.GetAllProductsAsync());
}

MapEndpoints(app);
```
