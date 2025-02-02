# Statik Faylların Optimizasiyası

Statik fayllar (CSS, JavaScript, şəkillər) bir veb tətbiqinin əsas tikinti bloklarıdır. Yanlış qurulmuş statik fayl idarəetməsi, tətbiq performansını aşağı sala və pis bir istifadəçi təcrübəsinə yol aça bilər.

---

## 1. Statik Faylları Birbaşa Serve Etmək

❌ **Yanlış İstifadə:** Statik faylları manual olaraq işləmək.

```csharp
app.MapGet("/static/{filename}", async (HttpContext context, string filename) =>
{
    var filePath = Path.Combine("wwwroot", filename);
    if (File.Exists(filePath))
    {
        await context.Response.SendFileAsync(filePath);
    }
    else
    {
        context.Response.StatusCode = 404;
    }
});
```

✅ **İdeal İstifadə:** `UseStaticFiles` middleware-ini istifadə edərək statik faylları serve edin.

```csharp
app.UseStaticFiles();
```

---

## 2. Gzip və ya Brotli Sıxıştırmanı Nəzərə Almamaq

❌ **Yanlış İstifadə:** Sıxıştırma olmadan böyük faylları serve etmək.

```plaintext
Bütün fayllar sıxıştırılmadan göndərilir.
```

✅ **İdeal İstifadə:** `ResponseCompression` middleware-ini aktivləşdirin.

```csharp
app.UseResponseCompression();

builder.Services.AddResponseCompression(options =>
{
    options.EnableForHttps = true;
    options.Providers.Add<GzipCompressionProvider>();
    options.Providers.Add<BrotliCompressionProvider>();
});
```

---

## 3. Cache-Control və ETag Başlıqlarının Əskikliyi

❌ **Yanlış İstifadə:** Statik fayllar üçün keşləmə başlıqlarını göstərməmək.

```plaintext
Cache-Control başlığı olmadan fayllar göndərilir.
```

✅ **İdeal İstifadə:** Keşləmə başlıqlarını qurun.

```csharp
app.UseStaticFiles(new StaticFileOptions
{
    OnPrepareResponse = context =>
    {
        context.Context.Response.Headers["Cache-Control"] = "public,max-age=31536000";
        context.Context.Response.Headers["ETag"] = ""unique-id"";
    }
});
```

---

## 4. Yüksək Keyfiyyətli Şəkillərin Optimizə Edilməməsi

❌ **Yanlış İstifadə:** Böyük və optimizə edilməmiş şəkilləri serve etmək.

```plaintext
images/background.png (5MB)
```

✅ **İdeal İstifadə:** Şəkilləri sıxışdıraraq və CDN istifadə edərək optimizə edin.

- **Alətlər:** ImageMagick, TinyPNG
- **Nümunə:** Şəkilləri bir CDN üzərindən paylamaq.

```plaintext
https://cdn.example.com/images/background.png
```

---

## 5. Təhlükəsizlik Başlıqlarının Əskikliyi

❌ **Yanlış İstifadə:** Statik fayllar üçün təhlükəsizlik başlıqlarını nəzərə almamaq.

```plaintext
Statik fayllar X-Content-Type-Options başlığı olmadan göndərilir.
```

✅ **İdeal İstifadə:** Təhlükəsizlik başlıqlarını qurun.

```csharp
app.UseStaticFiles(new StaticFileOptions
{
    OnPrepareResponse = context =>
    {
        context.Context.Response.Headers["X-Content-Type-Options"] = "nosniff";
        context.Context.Response.Headers["Content-Security-Policy"] = "default-src 'self'";
    }
});
```

---

## 6. CDN İstifadəsinin Nəzərə Alınmaması

❌ **Yanlış İstifadə:** Bütün statik faylları birbaşa serverdən serve etmək.

```plaintext
Bütün fayllar serverdən yüklənir.
```

✅ **İdeal İstifadə:** Statik faylları bir CDN ilə paylayın.

```plaintext
https://cdn.example.com/styles/main.css
https://cdn.example.com/scripts/app.js
```

---

## 7. Həddindən Artıq Sayda HTTP Sorğusu

❌ **Yanlış İstifadə:** Həddindən artıq sayda kiçik faylı ayrı-ayrılıqda serve etmək.

```html
<link rel="stylesheet" href="/css/reset.css">
<link rel="stylesheet" href="/css/grid.css">
<link rel="stylesheet" href="/css/theme.css">
```

✅ **İdeal İstifadə:** Faylları birləşdirərək HTTP sorğularını azaldın.

```html
<link rel="stylesheet" href="/css/styles.bundle.css">
```
