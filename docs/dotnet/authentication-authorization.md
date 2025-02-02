# Authentication və Authorization

Authentication (kimlik doğrulama) və authorization (yetki vermək, icazə vermək), müasir veb tətbiqlərinin əsas təhlükəsizlik komponentləridir. Yanlış tətbiqləri təhlükəsizlik açıqlarına, performans problemlərinə və istifadəçi təcrübəsi problemlərinə yol aça bilər.

---

## 1. JWT-nin Yanlış İstifadəsi

❌ **Yanlış İstifadə:** JWT-ni saxlamaq üçün `localStorage` istifadə etmək.

```javascript
localStorage.setItem("jwt", token); // Təhlükəsizlik açığına səbəb ola bilər
```

✅ **Düzgün İstifadə:** JWT-ni `HttpOnly` cookie olaraq saxlayaraq XSS hücumlarının qarşısını alın.

```csharp
var cookieOptions = new CookieOptions
{
    HttpOnly = true,
    Secure = true,
    SameSite = SameSiteMode.Strict
};
Response.Cookies.Append("jwt", token, cookieOptions);
```

---

## 2. Səhv İcazə Nəzarətləri

❌ **Yanlış İstifadə:** Yetkilendirmə nəzarətlərini müştəri tərəfində həyata keçirmək.

```javascript
if (user.role === "admin") {
    // Yetkili əməliyyatlar
}
```

✅ **Düzgün İstifadə:** Yetkilendirme nəzarətlərini server tərəfində həyata keçirin.

```csharp
[Authorize(Roles = "Admin")]
public IActionResult AdminEndpoint()
{
    return Ok("Yalnızca admin istifadəçilər daxil ola bilər.");
}
```

---

## 3. Şifrələrin Yanlış İdarəedilməsi

❌ **Yanlış İstifadə:** Şifrələri düz mətn (plaintext) olaraq saxlamaq.

```sql
INSERT INTO Users (Username, Password) VALUES ('user1', '123456');
```

✅ **Düzgün İstifadə:** Şifrələri hash'ləyərək təhlükəsiz bir şəkildə saxlayın.

```csharp
var hashedPassword = BCrypt.Net.BCrypt.HashPassword("123456");
```

---

## 4. Təhlükəsiz Olmayan Standart Konfiqurasiyalar

❌ **Yanlış İstifadə:** HTTPS-i məcburi etməmək.

```csharp
app.UseAuthentication();
```

✅ **Düzgün İstifadə:** HTTPS istifadəsini məcburi edin və təhlükəsiz konfiqurasiyalar tətbiq edin.

```csharp
app.UseHttpsRedirection();
app.UseAuthentication();
```

---

## 5. Expired Token İdarəetməsinin Nəzərə Almamaq

❌ **Yanlış İstifadə:** Müddəti bitən token'ları yoxlamamaq.

```csharp
var tokenHandler = new JwtSecurityTokenHandler();
var token = tokenHandler.ReadJwtToken(jwt);
```

✅ **Düzgün İstifadə:** Token etibarlılığını doğrulayın və müddəti bitən token'ları idarə edin.

```csharp
var validationParameters = new TokenValidationParameters
{
    ValidateIssuer = true,
    ValidateAudience = true,
    ValidateLifetime = true,
    IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes("secret_key"))
};
tokenHandler.ValidateToken(jwt, validationParameters, out SecurityToken validatedToken);
```

---

## 6. Role-Based Authorization Yanlış İstifadəsi

❌ **Yanlış İstifadə:** Rolları sabit kodlaşdırmaq.

```csharp
if (user.Role == "Admin")
{
    // Yetkili əməliyyatlar
}
```

✅ **Düzgün İstifadə:** Policy-based authorization tətbiq edin.

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("AdminOnly", policy => policy.RequireRole("Admin"));
});

[Authorize(Policy = "AdminOnly")]
public IActionResult AdminEndpoint()
{
    return Ok("Yalnızca admin istifadəçilər daxil ola bilər.");
}
```

---

## 7. Open Redirect Təhlükəsizlik Açıqları

❌ **Yanlış İstifadə:** Redirect URL-lərini doğrulamadan yönləndirmək.

```csharp
return Redirect(returnUrl); // Təhlükəsizlik açığı
```

✅ **Düzgün İstifadə:** Yönləndirmə URL-lərini doğrulayın.

```csharp
if (Url.IsLocalUrl(returnUrl))
{
    return Redirect(returnUrl);
}
return RedirectToAction("Index", "Home");
```

---

## 8. İstifadəçi Sessiyalarının Pis İdarəedilməsi

❌ **Yanlış İstifadə:** İstifadəçi sessiyalarını əllə idarə etmək.

```csharp
HttpContext.Session.SetString("User", "user1");
```

✅ **Düzgün İstifadə:** Identity framework kimi standart sessiya idarəetmə alətlərini istifadə edin.

```csharp
services.AddIdentity<ApplicationUser, IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>()
    .AddDefaultTokenProviders();
```
