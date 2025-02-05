# Routing və URL İdarəetməsi

Routing və URL idarəetməsi, veb tətbiqlərinin düzgün işləməsi və istifadəçi təcrübəsinin artırılması üçün kritik əhəmiyyətə malikdir. Yanlış qurulmuş yönləndirmə, performans problemlərinə və səhv nəticələrə yol aça bilər.

---

## 1. Yanlış Route Təyinləri

❌ **Yanlış İstifadə:** Ziddiyyətli və ya qarışıqlıq yaradan routelar.

```csharp
app.MapGet("/products", () => "Bütün məhsullar");
app.MapGet("/products/{id}", (int id) => $"Məhsul ID: {id}");
app.MapGet("/products/{category}", (string category) => $"Kateqoriya: {category}"); // Ziddiyyət
```

✅ **Düzgün İstifadə:** Routeları açıq şəkildə təyin edin və Ziddiyyətləri önləyin.

```csharp
app.MapGet("/products", () => "Bütün məhsullar");
app.MapGet("/products/{id:int}", (int id) => $"Məhsul ID: {id}");
app.MapGet("/products/category/{category}", (string category) => $"Kateqoriya: {category}");
```

---

## 2. Gərəksiz Route Parametrləri

❌ **Yanlış İstifadə:** Route parametrlərini gərəksiz yerə istifadə etmək.

```csharp
app.MapGet("/user/{id}/details", (int id) => $"İstifadəçi ID: {id}");
```

✅ **Düzgün İstifadə:** Route parametrlərini mənalı və minimum səviyyədə saxlayın.

```csharp
app.MapGet("/users/{id}", (int id) => $"İstifadəçi ID: {id}");
```

---

## 3. Catch-All Route İstifadəsi

❌ **Yanlış İstifadə:** Catch-all routeları diqqətsizliklə istifadə etmək.

```csharp
app.MapGet("/{*path}", (string path) => $"Path: {path}"); // Digər routeları əngəlləyə bilər
```

✅ **Düzgün İstifadə:** Catch-all routeları diqqətlə və müəyyən bir məqsədə yönəlik istifadə edin.

```csharp
app.MapGet("/files/{*filepath}", (string filepath) => $"Fayl yolu: {filepath}");
```

---

## 4. Route Adlarının İstifadə Edilməməsi

❌ **Yanlış İstifadə:** Route adlarını göstərmədən URL-lərlə işləmək.

```csharp
app.MapGet("/home", () => "Ana Səhifə");
```

✅ **Düzgün İstifadə:** Route adlarını göstərərək yönləndirmə əməliyyatlarını daha oxunaqlı hala gətirin.

```csharp
app.MapGet("/home", () => "Ana Səhifə").WithName("Home");
```

---

## 5. Query Parametrlərinin Yanlış İstifadəsi

❌ **Yanlış İstifadə:** Query parametrlərini manual olaraq işləmək.

```csharp
app.MapGet("/search", (HttpContext context) =>
{
    var query = context.Request.Query["q"];
    return $"Axtarış: {query}";
});
```

✅ **Düzgün İstifadə:** Query parametrlərini birbaşa metod parametri olaraq bağlayın.

```csharp
app.MapGet("/search", (string q) => $"Axtarış: {q}");
```

---

## 6. Route Constraint Əskikliyi

❌ **Yanlış İstifadə:** Route parametrlərini yoxlama etmədən istifadə etmək.

```csharp
app.MapGet("/users/{id}", (string id) => $"İstifadəçi ID: {id}");
```

✅ **Düzgün İstifadə:** Route constraintləri istifadə edərək doğru verilən tiplərini təyin edin.

```csharp
app.MapGet("/users/{id:int}", (int id) => $"İstifadəçi ID: {id}");
```

---

## 7. Route Prioritization Problemləri

❌ **Yanlış İstifadə:** Xüsusi routeları ümumi routeların altına yerləşdirmək.

```csharp
app.MapGet("/{category}", (string category) => $"Kateqoriya: {category}");
app.MapGet("/products", () => "Məhsul siyahısı"); // İşləməz
```

✅ **Düzgün İstifadə:** Daha xüsusi routeları əvvəl təyin edin.

```csharp
app.MapGet("/products", () => "Məhsul siyahısı");
app.MapGet("/{category}", (string category) => $"Kateqoriya: {category}");
```
