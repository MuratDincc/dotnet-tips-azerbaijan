# Fallback Strategiyaları: Yanlış və Düzgün İstifadə  

Fallback strategiyaları, bir əməliyyat uğursuz olduqda alternativ həll təqdim etməyi hədəfləyir. Polly, xəta hallarında sistemin düzgün işləməyə davam etməsini təmin edən güclü fallback mexanizmləri təqdim edir.  

---

## 1. Əsas Fallback İstifadəsi  

Fallback strategiyası, müəyyən bir əməliyyat uğursuz olduqda bir standart dəyər qaytarmağa imkan verir.  

❌ **Yanlış İstifadə:** Xətaları idarə etmədən standart dəyər qaytarmaq.  

```csharp
try
{
    var result = await MakeHttpRequestAsync();
}
catch
{
    var result = "Default Value"; // Nəzarətsiz fallback
}
```

✅ **Düzgün İstifadə:** Polly ilə fallback strategiyası təyin edin.  

```csharp
var fallbackPolicy = Policy<string>
    .Handle<HttpRequestException>()
    .FallbackAsync("Default Value");

var result = await fallbackPolicy.ExecuteAsync(() => MakeHttpRequestAsync());
```

---

## 2. Fallback və Retry Kombinasiyası  

Fallback və Retry siyasətlərini birləşdirərək daha dayanıqlı həll yarada bilərsiniz.  

✅ **Nümunə:**  

```csharp
var retryPolicy = Policy
    .Handle<HttpRequestException>()
    .WaitAndRetryAsync(3, retryAttempt => TimeSpan.FromSeconds(retryAttempt));

var fallbackPolicy = Policy<string>
    .Handle<HttpRequestException>()
    .FallbackAsync("Fallback Value");

var combinedPolicy = Policy.WrapAsync(fallbackPolicy, retryPolicy);

var result = await combinedPolicy.ExecuteAsync(() => MakeHttpRequestAsync());
```

---

## 3. Fallback Strategiyası ilə Dinamik Cavablar  

Fallback zamanı dinamik olaraq hesablanan bir cavabı qaytara bilərsiniz.  

✅ **Nümunə:**  

```csharp
var fallbackPolicy = Policy<string>
    .Handle<HttpRequestException>()
    .FallbackAsync(
        fallbackAction: async (cancellationToken) =>
        {
            Console.WriteLine("Fallback aktiv edildi. Standart dəyər qaytarılır.");
            return await Task.FromResult("Dinamik Hesablanmış Dəyər");
        });

var result = await fallbackPolicy.ExecuteAsync(() => MakeHttpRequestAsync());
```

---

## 4. Performans və Monitorinq  

- **Loglama:** Fallback işə düşdükdə loglama edərək hansı hallarda aktivləşdiyini izləyin.  
- **Metriklər:** Hansı əməliyyatların fallback strategiyasına ehtiyac duyduğunu ölçmək üçün metriklər əlavə edin.  

✅ **Nümunə:**  

```csharp
var fallbackPolicy = Policy<string>
    .Handle<HttpRequestException>()
    .FallbackAsync(
        fallbackAction: async (cancellationToken) =>
        {
            Console.WriteLine("Xəta səbəbilə fallback istifadə olunur.");
            return "Fallback Nəticəsi";
        },
        onFallbackAsync: async (exception, context) =>
        {
            Console.WriteLine($"Fallback işə düşdü: {exception.Exception.Message}");
            await Task.CompletedTask;
        });

var result = await fallbackPolicy.ExecuteAsync(() => MakeHttpRequestAsync());
```