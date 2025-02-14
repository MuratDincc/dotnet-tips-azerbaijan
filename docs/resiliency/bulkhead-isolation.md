# Bulkhead Isolation: Yanlış və Düzgün İstifadə  

Bulkhead Isolation, sistemdəki resursların müəyyən bir hissəsini ayıraraq bir komponentin sıradan çıxmasının digər komponentlərə təsir etməsinin qarşısını almağı hədəfləyir. Polly, bu strategiyanı tətbiq etmək üçün effektiv alətlər təqdim edir.  

---

## 1. Bulkhead Isolation-ın Əhəmiyyəti  

Bulkhead Isolation, bir sistemdəki xətaların yayılmasının qarşısını alır. Məsələn, intensiv API çağırış trafiki digər proseslərin performansını azaltmamalıdır.  

❌ **Yanlış İstifadə:** Resursların nəzarətsiz paylaşılması.  

```csharp
Parallel.For(0, 100, async i =>
{
    await MakeHttpRequestAsync(); // Bütün çağırışlar limitsiz resurs istehlak edir
});
```

✅ **Düzgün İstifadə:** Polly ilə resurs limitlərini nəzarət altına alın.  

```csharp
var bulkheadPolicy = Policy.BulkheadAsync(10, int.MaxValue);

await bulkheadPolicy.ExecuteAsync(() => MakeHttpRequestAsync());
```

---

## 2. Bulkhead ilə Növbələmə  

Bulkhead Isolation, həddən artıq yüklənmə halında daxil olan prosesləri bir növbəyə ala bilər.  

✅ **Nümunə:**  

```csharp
var bulkheadPolicy = Policy.BulkheadAsync(
    maxParallelization: 10,
    maxQueuingActions: 20,
    onBulkheadRejectedAsync: context =>
    {
        Console.WriteLine("Sorğu bulkhead limiti səbəbilə rədd edildi.");
        return Task.CompletedTask;
    });

await bulkheadPolicy.ExecuteAsync(() => MakeHttpRequestAsync());
```

---

## 3. Bulkhead və Digər Siyasətlərlə Kombinasiyası  

Bulkhead Isolation, digər Polly siyasətləri ilə birləşdirilərək daha dayanıqlı sistemlər qurula bilər.  

✅ **Nümunə:** Retry və Circuit Breaker ilə Bulkhead istifadəsi.  

```csharp
var retryPolicy = Policy
    .Handle<HttpRequestException>()
    .WaitAndRetryAsync(3, retryAttempt => TimeSpan.FromSeconds(retryAttempt));

var circuitBreakerPolicy = Policy
    .Handle<HttpRequestException>()
    .CircuitBreakerAsync(2, TimeSpan.FromSeconds(30));

var bulkheadPolicy = Policy.BulkheadAsync(10, 20);

var combinedPolicy = Policy.WrapAsync(retryPolicy, circuitBreakerPolicy, bulkheadPolicy);

await combinedPolicy.ExecuteAsync(() => MakeHttpRequestAsync());
```

---

## 4. Performans və Monitorinq  

- **Metrik Monitorinq:** Bulkhead limitlərinə çatılıb-çatılmadığını izləyin.  
- **Düzgün Parametr Seçimi:** `maxParallelization` və `maxQueuingActions` dəyərlərini sistemin tutumuna uyğun təyin edin.  

✅ **Nümunə:**  

```csharp
var bulkheadPolicy = Policy.BulkheadAsync(
    maxParallelization: 5,
    maxQueuingActions: 10,
    onBulkheadRejectedAsync: context =>
    {
        Console.WriteLine("Bulkhead limiti keçildi. Sorğu rədd edildi.");
        return Task.CompletedTask;
    });

await bulkheadPolicy.ExecuteAsync(() => MakeHttpRequestAsync());
```