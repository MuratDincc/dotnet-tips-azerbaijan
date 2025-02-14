# Circuit Breaker Vəziyyət İdarəetməsi  

Circuit Breaker, bir xidmətin xətaları nəzarətdə saxlamasını təmin edərək sistemin həddindən artıq yüklənməsinin qarşısını alan dayanıqlılıq mexanizmidir. Polly ilə Circuit Breaker vəziyyətlərini idarə etmək, daha dəqiq və effektiv xəta nəzarəti təmin edir.  

---

## 1. Circuit Breaker Nədir?  

Circuit Breaker sistemi üç əsas vəziyyətdə işlədə bilər:  
- **Closed (Qapalı):** Bütün sorğular icra olunur və xəta nisbəti izlənir.  
- **Open (Açıq):** Xəta həddi aşılarsa, bütün sorğular rədd edilir.  
- **Half-Open (Yarı Açıq):** Məhdud sayda sorğu icazə alır və sistemin vəziyyətinə görə yenidən `Closed` və ya `Open` rejiminə keçə bilər.  

---

## 2. Polly ilə Circuit Breaker İstifadəsi  

Polly ilə Circuit Breaker mexanizmini quraraq sistemi həddindən artıq yüklənmədən qoruya bilərsiniz.  

✅ **Nümunə:** Sadə Circuit Breaker  

```csharp
var circuitBreakerPolicy = Policy
    .Handle<Exception>()
    .CircuitBreakerAsync(
        exceptionsAllowedBeforeBreaking: 3,
        durationOfBreak: TimeSpan.FromSeconds(30));

await circuitBreakerPolicy.ExecuteAsync(async () =>
{
    Console.WriteLine("Sorğu icra edilir...");
    await Task.Delay(100); // Əsl iş məntiqi
    throw new Exception("Simulyasiya olunmuş xəta!");
});
```

---

## 3. Circuit Breaker Vəziyyətlərinin İdarəedilməsi  

Polly, Circuit Breaker vəziyyətlərini izləmək və hadisələrə cavab vermək üçün istifadə edilə bilən geri bildirim mexanizmləri təqdim edir.  

✅ **Nümunə:** Vəziyyət Dəyişikliklərini İzləmə  

```csharp
var circuitBreakerPolicy = Policy
    .Handle<Exception>()
    .CircuitBreakerAsync(
        exceptionsAllowedBeforeBreaking: 3,
        durationOfBreak: TimeSpan.FromSeconds(30),
        onBreak: (exception, timespan) =>
        {
            Console.WriteLine($"Circuit {timespan.TotalSeconds} saniyə müddətinə açıldı: {exception.Message}");
        },
        onReset: () =>
        {
            Console.WriteLine("Circuit qapalı vəziyyətə qaytarıldı.");
        },
        onHalfOpen: () =>
        {
            Console.WriteLine("Circuit yarı açıq vəziyyətdədir. Sistem sağlamlığı yoxlanılır...");
        });

await circuitBreakerPolicy.ExecuteAsync(async () =>
{
    Console.WriteLine("Əməliyyat icra edilir...");
    await Task.Delay(100);
    throw new Exception("Simulyasiya olunmuş xəta.");
});
```

---

## 4. Half-Open Vəziyyətinin İdarəedilməsi  

`Half-Open` vəziyyətində məhdud sayda əməliyyat icra olunur. Uğurlu nəticələr, Circuit Breaker-in `Closed` vəziyyətinə qayıtmasını təmin edir.  

✅ **Nümunə:** Half-Open Testi  

```csharp
var circuitBreakerPolicy = Policy
    .Handle<Exception>()
    .CircuitBreakerAsync(
        exceptionsAllowedBeforeBreaking: 2,
        durationOfBreak: TimeSpan.FromSeconds(20),
        onHalfOpen: () =>
        {
            Console.WriteLine("Half-Open: Məhdud əməliyyatla sistem sağlamlığı yoxlanılır.");
        });

await circuitBreakerPolicy.ExecuteAsync(async () =>
{
    Console.WriteLine("Sistem sağlamlığı yoxlanılır...");
    await Task.Delay(200); // Test məntiqi
});
```

---

## 5. Circuit Breaker və Retry Kombinasiyası  

Circuit Breaker və Retry siyasətlərini birləşdirərək daha dayanıqlı həll yarada bilərsiniz.  

✅ **Nümunə:** Retry və Circuit Breaker  

```csharp
var retryPolicy = Policy
    .Handle<Exception>()
    .WaitAndRetryAsync(3, retryAttempt => TimeSpan.FromSeconds(retryAttempt));

var circuitBreakerPolicy = Policy
    .Handle<Exception>()
    .CircuitBreakerAsync(3, TimeSpan.FromSeconds(30));

var combinedPolicy = Policy.WrapAsync(retryPolicy, circuitBreakerPolicy);

await combinedPolicy.ExecuteAsync(async () =>
{
    Console.WriteLine("Birləşdirilmiş siyasət icra edilir...");
    await Task.Delay(150);
    throw new Exception("Simulyasiya olunmuş xəta.");
});
```
