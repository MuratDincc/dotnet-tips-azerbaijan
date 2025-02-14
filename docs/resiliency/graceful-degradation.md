# Graceful Degradation (Tədrici Azalma)  

Graceful Degradation, bir sistemin qismən xəta vəziyyətində belə xidmət göstərməyə davam etməsini təmin edən dayanıqlılıq strategiyasıdır. Bu strategiya, kritik olmayan xüsusiyyətlərin müvəqqəti olaraq deaktiv edilməsini və ya azaldılmış xidmət təqdim edilməsini əhatə edir.  

---

## 1. Graceful Degradation Nədir?  

- **Məqsəd:** İstifadəçi təcrübəsini qoruyaraq sistemin tamamilə çökməsinin qarşısını almaq.  
- **İstifadə Sahələri:** Yüksək trafikə malik veb saytlar, mikroservis memarlığı, paylanmış sistemlər.  

Məsələn, bir e-ticarət saytında ödəniş xidməti müvəqqəti olaraq işləmirsə, istifadəçilərin məhsul baxışı və səbətə əlavə etmə funksiyalarından istifadə etməyə davam etməsi təmin edilə bilər.  

---

## 2. Polly ilə Graceful Degradation  

Polly-nin **Fallback** siyasəti, Graceful Degradation strategiyasını tətbiq etmək üçün effektiv bir vasitədir.  

✅ **Nümunə:** Fallback ilə Alternativ Cavab Qaytarma  

```csharp
var fallbackPolicy = Policy<string>
    .Handle<Exception>()
    .FallbackAsync(
        fallbackValue: "Azaldılmış funksionallıq: Zəhmət olmasa bir az sonra yenidən cəhd edin.",
        onFallbackAsync: async (exception, context) =>
        {
            Console.WriteLine($"Fallback aktiv edildi: {exception.Message}");
            await Task.CompletedTask;
        });

var result = await fallbackPolicy.ExecuteAsync(async () =>
{
    throw new Exception("Əsas xidmət əlçatmazdır!");
});

Console.WriteLine($"Cavab: {result}");
```

---

## 3. Xüsusiyyət Azaldılması (Feature Reduction)  

Sistem bəzi funksionallıqları müvəqqəti olaraq deaktiv edərək fəaliyyət göstərməyə davam edə bilər.  

✅ **Nümunə:** Kritik Olmayan Xüsusiyyətləri Deaktiv Etmək  

```csharp
if (!IsCriticalFeatureAvailable())
{
    Console.WriteLine("Kritik xüsusiyyət əlçatmazdır. Məhdud funksionallıq göstərilir.");
    DisplayBasicFeatures();
}
else
{
    DisplayFullFeatures();
}
```

---

## 4. Graceful Degradation ilə Keş İstifadəsi  

Müvəqqəti xəta halında, əvvəlcədən keşlənmiş məlumatlardan istifadə edilə bilər.  

✅ **Nümunə:** Keş ilə Ehtiyat Məlumat  

```csharp
var fallbackPolicy = Policy<string>
    .Handle<Exception>()
    .FallbackAsync(
        fallbackAction: async cancellationToken =>
        {
            Console.WriteLine("Xəta səbəbilə keşlənmiş məlumat istifadə olunur.");
            return "Keşlənmiş Məlumat";
        });

var result = await fallbackPolicy.ExecuteAsync(async () =>
{
    throw new Exception("Əsas xidmət uğursuz oldu!");
});

Console.WriteLine($"Nəticə: {result}");
```

---

## 5. Graceful Degradation və Retry Kombinasiyası  

Xətaları idarə etmək üçün Retry və Graceful Degradation strategiyalarını birləşdirə bilərsiniz.  

✅ **Nümunə:** Retry və Fallback Kombinasiyası  

```csharp
var retryPolicy = Policy
    .Handle<Exception>()
    .WaitAndRetryAsync(3, retryAttempt => TimeSpan.FromSeconds(retryAttempt));

var fallbackPolicy = Policy<string>
    .Handle<Exception>()
    .FallbackAsync(
        fallbackValue: "Xidmət müvəqqəti olaraq əlçatmazdır.");

var combinedPolicy = Policy.WrapAsync(fallbackPolicy, retryPolicy);

var result = await combinedPolicy.ExecuteAsync(async () =>
{
    throw new Exception("Xidmət uğursuz oldu!");
});

Console.WriteLine($"Cavab: {result}");
```

---

## 6. Performans və Monitorinq  

Graceful Degradation zamanı sistem performansını izləmək vacibdir:  
- **Metriklər:** Azaldılmış xidmətlərin nə qədər tez-tez istifadə edildiyini izləyin.  
- **Loglama:** Fallback və digər strategiyaların nə zaman işə düşdüyünü izləyin.  

✅ **Nümunə:** Loglama ilə Monitorinq  

```csharp
var fallbackPolicy = Policy<string>
    .Handle<Exception>()
    .FallbackAsync(
        fallbackValue: "Məhdud funksionallıq aktivdir.",
        onFallbackAsync: async (exception, context) =>
        {
            Console.WriteLine($"Fallback aktiv edildi: {exception.Message}");
            await Task.CompletedTask;
        });

await fallbackPolicy.ExecuteAsync(async () =>
{
    throw new Exception("Simulyasiya olunmuş xəta!");
});
```