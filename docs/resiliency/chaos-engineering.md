# Chaos Engineering (Xaos Mühəndisliyi)  

Chaos Engineering, bir sistemin dayanıqlılığını test etmək və xətalara qarşı necə davrandığını anlamaq üçün nəzərdə tutulmuş bir intizamdır. Polly ilə xəta simulyasiyası, gecikmə inyeksiyası və uğursuzluq faizlərini artırmaq kimi strategiyalar tətbiq edilə bilər.  

---

## 1. Polly ilə Şəbəkə Gecikməsi Simulyasiyası  

Gecikmə inyeksiya edərək sistemi yüksək gecikmə şərtlərində test edin.  

✅ **Nümunə: Təsadüfi Gecikmə Əlavə Etmə**  

```csharp
var latencyPolicy = Policy
    .InjectLatencyAsync(
        enabled: _ => true,
        injectionRate: 0.4, // %40 ehtimalla gecikmə
        latency: _ => TimeSpan.FromMilliseconds(new Random().Next(500, 2000))); // 500ms - 2000ms arası gecikmə

await latencyPolicy.ExecuteAsync(async () =>
{
    Console.WriteLine("Sorğu başladı...");
    await Task.Delay(100); // Əsl iş məntiqi
    Console.WriteLine("Sorğu tamamlandı.");
});
```

---

## 2. Polly ilə Xəta İnyeksiyası  

Müəyyən ehtimalla xəta yaradaraq sistemin necə reaksiya verdiyini analiz edin.  

✅ **Nümunə: Simulyasiya Edilmiş Xətalar**  

```csharp
var faultPolicy = Policy
    .InjectFaultAsync(
        enabled: _ => true,
        injectionRate: 0.3, // %30 ehtimalla xəta
        fault: _ => new Exception("Simulated fault!"));

await faultPolicy.ExecuteAsync(async () =>
{
    Console.WriteLine("Sorğu icra edilir...");
    await Task.Delay(200); // Əsl iş məntiqi
    Console.WriteLine("Sorğu uğurla tamamlandı.");
});
```

---

## 3. Polly ilə Gecikmə və Xəta Kombinasiyası  

Gecikmə və xəta inyeksiyasını birləşdirərək daha mürəkkəb ssenarilər yarada bilərsiniz.  

✅ **Nümunə: Kompleks Simulyasiya**  

```csharp
var combinedPolicy = Policy.WrapAsync(
    Policy.InjectLatencyAsync(
        enabled: _ => true,
        injectionRate: 0.3,
        latency: _ => TimeSpan.FromSeconds(2)), // 2 saniyə gecikmə
    Policy.InjectFaultAsync(
        enabled: _ => true,
        injectionRate: 0.2, // %20 ehtimalla xəta
        fault: _ => new Exception("Simulated fault!"))
);

await combinedPolicy.ExecuteAsync(async () =>
{
    Console.WriteLine("Əməliyyat icra edilir...");
    await Task.Delay(150); // Əsl iş məntiqi
    Console.WriteLine("Əməliyyat tamamlandı.");
});
```

---

## 4. Polly ilə İş Yükü Testi  

Sistemi yüksək yük altında test etmək üçün Chaos Engineering strategiyalarından istifadə edə bilərsiniz.  

✅ **Nümunə: Kütləvi Sorğular və Xəta Simulyasiyası**  

```csharp
var bulkPolicy = Policy.InjectFaultAsync(
    enabled: _ => true,
    injectionRate: 0.2,
    fault: _ => new Exception("Təsadüfi xəta inyeksiya edildi!"));

var tasks = Enumerable.Range(1, 50).Select(async i =>
{
    try
    {
        await bulkPolicy.ExecuteAsync(async () =>
        {
            Console.WriteLine($"Sorğu #{i} icra edilir");
            await Task.Delay(100);
            Console.WriteLine($"Sorğu #{i} tamamlandı.");
        });
    }
    catch (Exception ex)
    {
        Console.WriteLine($"Sorğu #{i} uğursuz oldu: {ex.Message}");
    }
});

await Task.WhenAll(tasks);
```

---

## 5. Polly ilə Graceful Degradation (Tədrici Azalma)  

Xidmətin tamamilə çökməsinin qarşısını almaq üçün xəta baş verdikdə məhdud bir xidmət təqdim edə bilərsiniz.  

✅ **Nümunə: Xəta Halında Standart Dəyər Qaytarma**  

```csharp
var fallbackPolicy = Policy<string>
    .Handle<Exception>()
    .FallbackAsync(
        fallbackValue: "Xəta səbəbindən standart dəyər qaytarılır",
        onFallbackAsync: async (result, context) =>
        {
            Console.WriteLine("Fallback aktiv edildi: Standart dəyər qaytarılır.");
            await Task.CompletedTask;
        });

var result = await fallbackPolicy.ExecuteAsync(async () =>
{
    Console.WriteLine("Sorğu icra edilir...");
    throw new Exception("Simulyasiya olunmuş xəta!"); // Xəta yaradılır
});

Console.WriteLine($"Nəticə: {result}");
```

---

## 6. Polly ilə Gecikməli Cavab və Retry Siyasətləri  

Xaotik şərtlər altında təkrar cəhdləri və gecikməli cavabları birləşdirin.  

✅ **Nümunə: Retry + Gecikmə**  

```csharp
var retryPolicy = Policy
    .Handle<Exception>()
    .WaitAndRetryAsync(3, retryAttempt => TimeSpan.FromSeconds(retryAttempt));

var latencyPolicy = Policy
    .InjectLatencyAsync(
        enabled: _ => true,
        injectionRate: 0.5, // %50 ehtimalla gecikmə
        latency: _ => TimeSpan.FromSeconds(1)); // 1 saniyə gecikmə

var combinedPolicy = Policy.WrapAsync(retryPolicy, latencyPolicy);

await combinedPolicy.ExecuteAsync(async () =>
{
    Console.WriteLine("Əməliyyat icra edilir...");
    await Task.Delay(200); // Əsl iş məntiqi
    Console.WriteLine("Əməliyyat tamamlandı.");
});
```