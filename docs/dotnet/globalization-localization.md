# Qloballaşma və Lokallaşdırma

Qloballaşma və lokallaşdırma, tətbiqinizin müxtəlif dillər və mədəniyyətlər(culture) üçün uyğun hala gətirilməsi prosesidir. Yanlış tətbiqlər istifadəçi təcrübəsinə mənfi təsir edə bilər və ya yanlış dil/format göstərilməsinə səbəb ola bilər.

---

## 1. Sabit Kodlanmış (Hardcoded) Mətnlərdən İstifadə Etmək

❌ **Yanlış İstifadə:** Mətnləri birbaşa sabit kodlamaq.

```csharp
public string GetWelcomeMessage()
{
    return "Welcome to our application!";
}
```

✅ **Düzgün İstifadə:** Resurs fayllarından istifadə edərək mətnləri lokallaşdırın.

**Resources/Texts.resx:**
```xml
<data name="WelcomeMessage" xml:space="preserve">
  <value>Welcome to our application!</value>
</data>
```

**İstifadə:**
```csharp
public string GetWelcomeMessage()
{
    return Resources.Texts.WelcomeMessage;
}
```

---

## 2. Tarix və Saat Formatlarını Sabit Kodlamaq

❌ **Yanlış İstifadə:** Tarix və saat formatlarını manual olaraq təyin etmək.

```csharp
var date = DateTime.Now.ToString("MM/dd/yyyy");
```

✅ **Düzgün İstifadə:** Culture məlumatlarından istifadə edərək tarix və saat formatlarını avtomatik hala gətirin.

```csharp
var date = DateTime.Now.ToString(CultureInfo.CurrentCulture);
```

---

## 3. `Thread.CurrentThread.CurrentCulture`'ı Birbaşa Dəyişdirmək

❌ **Yanlış İstifadə:** `Thread.CurrentThread.CurrentCulture`'ı manual olaraq dəyişdirmək.

```csharp
Thread.CurrentThread.CurrentCulture = new CultureInfo("fr-FR");
```

✅ **Düzgün İstifadə:** Middleware istifadə edərək culture parametrlərini idarə edin.

```csharp
app.UseRequestLocalization(new RequestLocalizationOptions
{
    DefaultRequestCulture = new RequestCulture("en-US"),
    SupportedCultures = new[] { new CultureInfo("en-US"), new CultureInfo("fr-FR") },
    SupportedUICultures = new[] { new CultureInfo("en-US"), new CultureInfo("fr-FR") }
});
```

---

## 4. İstifadəçi Seçimlərinə Görə Dil Ayarı Etməmək

❌ **Yanlış İstifadə:** Varsayılan dil parametrini bütün istifadəçilərə tətbiq etmək.

```csharp
var culture = new CultureInfo("en-US");
CultureInfo.DefaultThreadCurrentCulture = culture;
CultureInfo.DefaultThreadCurrentUICulture = culture;
```

✅ **Düzgün İstifadə:** İstifadəçinin üstünlük verdiyi dili nəzərə alın.

```csharp
app.Use(async (context, next) =>
{
    var userLanguage = context.Request.Headers["Accept-Language"].ToString();
    var culture = new CultureInfo(userLanguage);
    CultureInfo.CurrentCulture = culture;
    CultureInfo.CurrentUICulture = culture;

    await next();
});
```

---

## 5. Tərcümələrin Test Edilməməsi

❌ **Yanlış İstifadə:** Tərcümələrin müxtəlif dillərdə necə görünəcəyini test etməmək.

```plaintext
Test edilmədən lokallaşdırma edilir.
```

✅ **Düzgün İstifadə:** Müxtəlif dillərdə tərcümələri test edin.

- Tərcümələri test etmək üçün Visual Studio-da "Set as Startup Culture" xüsusiyyətini istifadə edə bilərsiniz.
- Həmçinin `CultureInfo`-ni manual olaraq dəyişdirə bilərsiniz:

```csharp
var culture = new CultureInfo("fr-FR");
CultureInfo.CurrentCulture = culture;
CultureInfo.CurrentUICulture = culture;
```

---

## 6. Verilənlər Bazasında Sabit Kodlanmış Məlumatlardan İstifadə Etmək

❌ **Yanlış İstifadə:** Verilənlər bazasında yalnız bir dildə məzmun saxlamaq.

```plaintext
ProductName: "Laptop"
```

✅ **Düzgün İstifadə:** Verilənlər bazasında çoxlu dil dəstəyi təmin edin.

```plaintext
ProductName_en: "Laptop"
ProductName_fr: "Ordinateur portable"
```

---

## 7. Lokallaşdırılmış Resursların Performansını İzləməmək

❌ **Yanlış İstifadə:** Lokallaşdırılmış resursların yüklənmə performansını nəzərə almamaq.

✅ **Düzgün İstifadə:** Performans izləmə alətləri istifadə edərək resursların yüklənmə sürətini analiz edin.

- **Nümunə:** Application Insights, Prometheus

```csharp
var startTime = Stopwatch.StartNew();
var message = Resources.Texts.WelcomeMessage;
startTime.Stop();
logger.LogInformation("Lokallaşdırılmış resurs {TimeTaken} ms-də yükləndi.", startTime.ElapsedMilliseconds);
```
