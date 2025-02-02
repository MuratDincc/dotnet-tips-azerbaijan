# Dinamik Konfiqurasiya: Yanlış və Düzgün İstifadə

Dinamik konfiqurasiya, tətbiqinizin konfiqurasiya parametrlərini, tətbiq işləyərkən dəyişdirmə qabiliyyəti təmin edir. Yanlış istifadə edilən dinamik konfiqurasiyalar, məlumat uyğunsuzluqlarına və gözlənilməz davranışlara yol aça bilər.

---

## 1. Sabit Kodlanmış Konfiqurasiyalar

❌ **Yanlış İstifadə:** Konfiqurasiyaları sabit kodlamaq.

```csharp
public class AppConfig
{
    public const string ConnectionString = "Server=localhost;Database=MyApp;User=admin;Password=1234;";
}
```

✅ **Düzgün İstifadə:** Konfiqurasiyaları bir faylda və ya `environment variables`da tutaraq dinamik hala gətirin.

**appsettings.json:**
```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=localhost;Database=MyApp;User=admin;Password=1234;"
  }
}
```

**İstifadə:**
```csharp
var connectionString = builder.Configuration.GetConnectionString("DefaultConnection");
```

---

## 2. Həssas Məlumatları Təhlükəsiz Şəkildə İdarə Etməmək

❌ **Yanlış İstifadə:** Həssas məlumatları konfiqurasiya fayllarında düz mətn olaraq saxlamaq.

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=localhost;Database=MyApp;User=admin;Password=1234;"
  }
}
```

✅ **Düzgün İstifadə:** Həssas məlumatları təhlükəsiz bir şəkildə idarə etmək üçün bir secret management aləti istifadə edin.

**HashiCorp Vault İstifadəsi:**

```csharp
builder.Configuration.AddVault(options =>
{
    options.Address = "https://vault.example.com";
    options.Token = Environment.GetEnvironmentVariable("VAULT_TOKEN");
});
```

**Azure Key Vault İstifadəsi:**

```csharp
builder.Configuration.AddAzureKeyVault(
    new Uri("https://mykeyvault.vault.azure.net/"),
    new DefaultAzureCredential()
);
```

---

## 3. Dinamik Konfiqurasiyanı İzləməmək

❌ **Yanlış İstifadə:** Konfiqurasiya dəyişikliklərini izləməmək.

✅ **Düzgün İstifadə:** Dinamik konfiqurasiya dəyişikliklərini izləmək üçün konfiqurasiya təminatçılarını istifadə edin.

**Azure App Configuration Nümunəsi:**
```csharp
builder.Configuration.AddAzureAppConfiguration(options =>
{
    options.Connect("ConnectionString")
           .ConfigureRefresh(refresh =>
           {
               refresh.Register("AppSettings:Sentinel", refreshAll: true);
           });
});
```

---

## 4. Performans Üzərindəki Təsirləri Göz Ardı Etmək

❌ **Yanlış İstifadə:** Konfiqurasiyaların davamlı olaraq oxunması.

```csharp
var setting = builder.Configuration["AppSettings:SettingKey"]; // Davamlı çağırışlar
```

✅ **Düzgün İstifadə:** Konfiqurasiyaları bir keş mexanizmi ilə optimallaşdırın.

```csharp
public class MyService
{
    private readonly IConfiguration _configuration;
    private string _cachedSetting;

    public MyService(IConfiguration configuration)
    {
        _configuration = configuration;
        _cachedSetting = _configuration["AppSettings:SettingKey"];
    }
}
```
