# Keçici Xətaların İdarə Edilməsi: Yanlış və Düzgün İstifadə  

Keçici xətalar (`transient faults`), şəbəkə bağlantıları və ya xarici xidmətlərlə əlaqə zamanı ara-sıra baş verən qısa müddətli problemlərdir. Polly, keçici xətaları idarə etmək üçün güclü alətlər təqdim edərək tətbiqlərinizin dayanıqlılığını artırır.  

---

## 1. Keçici Xəta Nədir?  

Keçici xətalar adətən yenidən cəhd etmə (`retry`) ilə həll edilə bilən qısa müddətli problemlərdir. Məsələn:  
- Zaman aşımı (`timeout`) xətaları  
- Müvəqqəti şəbəkə kəsintiləri  
- Xarici xidmətin müvəqqəti əlçatmaz olması  

❌ **Yanlış İstifadə:** Xətaları idarə etmədən əməliyyatları yenidən icra etmək.  

```csharp
try
{
    await MakeHttpRequestAsync();
}
catch
{
    await MakeHttpRequestAsync(); // Nəzarətsiz təkrar cəhd
}
```

✅ **Düzgün İstifadə:** Polly ilə `retry` siyasətini tətbiq edin.  

```csharp
var retryPolicy = Policy
    .Handle<HttpRequestException>()
    .WaitAndRetryAsync(3, retryAttempt => TimeSpan.FromSeconds(retryAttempt));

await retryPolicy.ExecuteAsync(() => MakeHttpRequestAsync());
```

---

## 2. Retry Siyasətləri  

Keçici xətaları idarə etmək üçün Polly ilə müxtəlif `retry` strategiyalarını təyin edə bilərsiniz.  

### **Sabit Gecikməli Retry:**  

✅ **Nümunə:**  

```csharp
var retryPolicy = Policy
    .Handle<HttpRequestException>()
    .WaitAndRetryAsync(3, _ => TimeSpan.FromSeconds(2));

await retryPolicy.ExecuteAsync(() => MakeHttpRequestAsync());
```

### **Artan Gecikməli Retry:**  

✅ **Nümunə:**  

```csharp
var retryPolicy = Policy
    .Handle<HttpRequestException>()
    .WaitAndRetryAsync(3, retryAttempt => TimeSpan.FromSeconds(Math.Pow(2, retryAttempt)));

await retryPolicy.ExecuteAsync(() => MakeHttpRequestAsync());
```

### **Jitter (Təsadüfi Gecikmə):**  

Jitter, sabit gecikmənin yaratdığı yük sıxlığını azaldır.  

✅ **Nümunə:**  

```csharp
var retryPolicy = Policy
    .Handle<HttpRequestException>()
    .WaitAndRetryAsync(3, retryAttempt =>
    {
        var jitter = new Random().NextDouble();
        return TimeSpan.FromSeconds(retryAttempt + jitter);
    });

await retryPolicy.ExecuteAsync(() => MakeHttpRequestAsync());
```

---

## 3. Timeout və Retry Kombinasiyası  

Timeout və retry siyasətlərini birləşdirərək daha dayanıqlı həll yarada bilərsiniz.  

✅ **Nümunə:**  

```csharp
var timeoutPolicy = Policy
    .TimeoutAsync(TimeSpan.FromSeconds(10));

var retryPolicy = Policy
    .Handle<HttpRequestException>()
    .WaitAndRetryAsync(3, retryAttempt => TimeSpan.FromSeconds(2));

var combinedPolicy = Policy.WrapAsync(retryPolicy, timeoutPolicy);

await combinedPolicy.ExecuteAsync(() => MakeHttpRequestAsync());
```

---

## 4. Performans və Monitorinq  

- **Loglama:** Retry əməliyyatlarını izləmək üçün loglar əlavə edin.  
- **Metriklər:** Hansı əməliyyatların keçici xətalara səbəb olduğunu müəyyən etmək üçün metriklər toplayın.  

✅ **Nümunə:**  

```csharp
var retryPolicy = Policy
    .Handle<HttpRequestException>()
    .WaitAndRetryAsync(3, retryAttempt =>
    {
        Console.WriteLine($"Yenidən cəhd edilir: {retryAttempt}...");
        return TimeSpan.FromSeconds(retryAttempt);
    });

await retryPolicy.ExecuteAsync(() => MakeHttpRequestAsync());
```