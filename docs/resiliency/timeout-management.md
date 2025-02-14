# Timeout İdarəetməsi  

Timeout, tətbiqlərdə uzunmüddətli əməliyyatları məhdudlaşdırmaq və istifadəçi təcrübəsini qorumaq üçün vacib bir strategiyadır. Polly, əməliyyatlarınızda zaman aşımı nəzarəti üçün effektiv bir həll təqdim edir.  

---

## 1. Əsas Timeout İdarəetməsi  

❌ **Yanlış İstifadə:** Timeout nəzarəti olmadan uzun müddət gözləmək.  

```csharp
await MakeHttpRequestAsync(); // Potensial olaraq sonsuza qədər gözləyə bilər
```

✅ **Düzgün İstifadə:** Polly ilə zaman aşımı nəzarəti əlavə edin.  

```csharp
var timeoutPolicy = Policy
    .TimeoutAsync(TimeSpan.FromSeconds(5));

await timeoutPolicy.ExecuteAsync(() => MakeHttpRequestAsync());
```

---

## 2. Timeout və Retry Kombinasiyası  

Timeout və Retry siyasətlərini birləşdirərək həm əməliyyatı müəyyən bir müddətlə məhdudlaşdıra, həm də xəta halında yenidən cəhd edə bilərsiniz.  

✅ **Nümunə:**  

```csharp
var retryPolicy = Policy
    .Handle<HttpRequestException>()
    .WaitAndRetryAsync(3, retryAttempt => TimeSpan.FromSeconds(retryAttempt));

var timeoutPolicy = Policy
    .TimeoutAsync(TimeSpan.FromSeconds(5));

var combinedPolicy = Policy.WrapAsync(retryPolicy, timeoutPolicy);

await combinedPolicy.ExecuteAsync(() => MakeHttpRequestAsync());
```

---

## 3. `CancellationToken` ilə Timeout İdarəetməsi  

Polly, `CancellationToken` ilə inteqrasiya olunaraq əməliyyatları ləğv etməyə imkan verir.  

✅ **Nümunə:**  

```csharp
var cts = new CancellationTokenSource(TimeSpan.FromSeconds(5));

var timeoutPolicy = Policy
    .TimeoutAsync(TimeSpan.FromSeconds(10));

await timeoutPolicy.ExecuteAsync(
    async token => await MakeHttpRequestAsync(token),
    cts.Token);
```

---

## 4. Performans və Monitorinq  

- **Loglama:** Timeout halları izləmək üçün log əlavə edin.  
- **Uyarlanabilən Vaxtlar:** Müxtəlif əməliyyatlar üçün fərqli timeout müddətləri təyin edin.  

✅ **Nümunə:**  

```csharp
var timeoutPolicy = Policy
    .TimeoutAsync(TimeSpan.FromSeconds(5), (context, timespan, task) =>
    {
        Console.WriteLine($"Timeout {timespan.TotalSeconds} saniyədən sonra baş verdi.");
        return Task.CompletedTask;
    });

await timeoutPolicy.ExecuteAsync(() => MakeHttpRequestAsync());
```
