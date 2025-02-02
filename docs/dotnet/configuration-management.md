# Configuration İdarəetməsi 

Tətbiq parametrlərinin idarəsi, bir proqram təminatının fərqli mühitlərdə (development, test, production) düzgün şəkildə işləməsini təmin etmək üçün kritik əhəmiyyətə malikdir. Yanlış parametr idarəetməsi, təhlükəsizlik açıqlarına və ya tətbiq xətalarına yol aça bilər.

---

## 1. Sabit Kodlanmış (Hardcoded) Dəyərlər İstifadəsi

❌ **Yanlış İstifadə:** Parametri birbaşa kod içində təyin etmək.

```csharp
public class DatabaseConfig
{
    public string ConnectionString => "Server=localhost;Database=MyApp;User=admin;Password=password;";
}
```

✅ **Düzgün İstifadə:** `appsettings.json` və ya `enviroment variables` istifadə edərək parametrləri xaricləşdirin.

**appsettings.json:**
```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=localhost;Database=MyApp;User=admin;Password=password;"
  }
}
```

**İstifadə:**
```csharp
var builder = WebApplication.CreateBuilder(args);
var connectionString = builder.Configuration.GetConnectionString("DefaultConnection");
```

---

## 2. Environment Parametrinin Yetərsiz İdarəetməsi

❌ **Yanlış İstifadə:** Bütün mühitlər üçün eyni məntigi istifadə etmək.

```csharp
{
  "Environment": "Production",
  "Logging": {
    "LogLevel": "Information"
  }
}
```

✅ **Düzgün İstifadə:** Mühit əsaslı məntigləri ayrı fayllarla idarə edin.

**appsettings.Development.json:**
```json
{
  "Environment": "Development",
  "Logging": {
    "LogLevel": "Debug"
  }
}
```

**appsettings.Production.json:**
```json
{
  "Environment": "Production",
  "Logging": {
    "LogLevel": "Error"
  }
}
```

---

## 3. Gizli Məlumatların Açığa çıxması

❌ **Yanlış İstifadə:** API açarlarını və ya şifrələri açıq şəkildə saxlamaq.

```csharp
{
  "APIKey": "12345-secret-key"
}
```

✅ **Düzgün İstifadə:** Gizli məlumatları `environment variables` və ya təhlükəsiz bir xidmətdə saxlayın.

**İstifadə:**
```csharp
var apiKey = Environment.GetEnvironmentVariable("MY_APP_API_KEY");
```

---

## 4. Parametr Dəyişiklikləri üçün Yenidən Paylama Tələb Etmək

❌ **Yanlış İstifadə:** Parametr dəyişiklikləri üçün tətbiqin yenidən işə salınması.

```csharp
public class Config
{
    public string SomeSetting { get; set; } = "DefaultValue";
}
```

✅ **Düzgün İstifadə:** Dinamik Parametr yükləmələri edin.

**Nümunə:** Azure App Configuration və ya digər dinamik parametr alətlərini istifadə edin.

```csharp
builder.Configuration.AddAzureAppConfiguration("ConnectionString");
```

---

## 5. Lazımsız Mürəkkəblik Yaratmaq

❌ **Yanlış İstifadə:** Lazımsız parametr açarları və mürəkkəblik.

```json
{
  "AppSettings": {
    "Feature1": {
      "Enabled": true,
      "MaxItems": 10,
      "Timeout": 5000
    },
    "Feature2": {
      "Enabled": false,
      "MaxItems": 5,
      "Timeout": 2000
    }
  }
}
```

✅ **Düzgün İstifadə:** Sadə və oxunaqlı parametrlər yaradın.

```json
{
  "Features": [
    {
      "Name": "Feature1",
      "Enabled": true,
      "MaxItems": 10,
      "Timeout": 5000
    },
    {
      "Name": "Feature2",
      "Enabled": false,
      "MaxItems": 5,
      "Timeout": 2000
    }
  ]
}
```

---

## 6. `Environment Variables` Yanlış İstifadəsi

❌ **Yanlış İstifadə:** `Environment Variables` birbaşa və düzənsiz bir şəkildə oxumaq.

```csharp
var setting = Environment.GetEnvironmentVariable("MY_APP_SETTING");
```

✅ **Düzgün İstifadə:** Ətraf mühit dəyişənlərini parametrlər ilə birləşdirin.

```csharp
builder.Configuration.AddEnvironmentVariables();
var setting = builder.Configuration["MY_APP_SETTING"];
```
