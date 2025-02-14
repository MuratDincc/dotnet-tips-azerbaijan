# API Gateway ilə Dayanıqlılıq (Resiliency)  

API Gateway, mikroservis memarlığında bir giriş nöqtəsi kimi xidmət edərək tələbləri yönləndirir, təhlükəsizliyi təmin edir və dayanıqlılığı artırır. Resiliency tətbiqlərində API Gateway istifadəsi, sistemin ümumi sabitliyini və miqyaslana bilməsini artırır.  

---

## 1. API Gateway Nədir?  

API Gateway, bir müştəri ilə mikroservislər arasında vasitəçi qat kimi işləyir.  
- **Funksiyalar:** Sorğu yönləndirmə, yük balanslama, təhlükəsizlik nəzarəti, xəta idarəetməsi.  
- **Üstünlüklər:** Mərkəzləşdirilmiş xəta idarəetməsi, performans optimizasiyası, təhlükəsizlik.  

---

## 2. Polly ilə API Gateway Dayanıqlılıq Strategiyaları  

API Gateway-də Polly istifadə edərək xəta idarəetmə və dayanıqlılıq mexanizmləri qura bilərsiniz.  

### **Retry (Təkrar Cəhd) Siyasəti**  

✅ **Nümunə:** Polly ilə təkrar cəhd mexanizmi  

```csharp
var retryPolicy = Policy
    .Handle<HttpRequestException>()
    .WaitAndRetryAsync(3, retryAttempt => TimeSpan.FromSeconds(retryAttempt));

await retryPolicy.ExecuteAsync(async () =>
{
    Console.WriteLine("Mikroservisə sorğu göndərilir...");
    await ForwardRequestToMicroserviceAsync();
});
```

---

### **Circuit Breaker İstifadəsi**  

Circuit Breaker, xətalı bir servisin istək sayını məhdudlaşdıraraq sistemi qoruyur.  

✅ **Nümunə:** Circuit Breaker ilə xəta idarəetməsi  

```csharp
var circuitBreakerPolicy = Policy
    .Handle<HttpRequestException>()
    .CircuitBreakerAsync(
        exceptionsAllowedBeforeBreaking: 3,
        durationOfBreak: TimeSpan.FromSeconds(30));

await circuitBreakerPolicy.ExecuteAsync(async () =>
{
    Console.WriteLine("Sorğu icra edilir...");
    await ForwardRequestToMicroserviceAsync();
});
```

---

## 3. Cache İstifadəsi ilə Performansın Yaxşılaşdırılması  

API Gateway-də tez-tez istifadə olunan məlumatları keşləməklə həm performansı artıra, həm də mikroservislərin yükünü azalda bilərsiniz.  

✅ **Nümunə:** Polly ilə Keş  

```csharp
var cachePolicy = Policy.CacheAsync<string>(
    cacheProvider: new MemoryCacheProvider(new MemoryCache(new MemoryCacheOptions())),
    ttl: TimeSpan.FromMinutes(5));

await cachePolicy.ExecuteAsync(async context =>
{
    return await GetDataFromMicroserviceAsync();
}, new Context("cache-key"));
```

---

## 4. Timeout İdarəetməsi  

API Gateway, mikroservislərdən cavab gözləyərkən müəyyən bir müddət ərzində əməliyyatı dayandırmalıdır.  

✅ **Nümunə:** Polly ilə Timeout Siyasəti  

```csharp
var timeoutPolicy = Policy
    .TimeoutAsync(TimeSpan.FromSeconds(10));

await timeoutPolicy.ExecuteAsync(async () =>
{
    Console.WriteLine("Mikroservisə timeout ilə sorğu göndərilir...");
    await ForwardRequestToMicroserviceAsync();
});
```

---

## 5. Load Balancing və Rate Limiting  

API Gateway, yük balanslama və sorğu məhdudlaşdırma mexanizmlərini effektiv şəkildə istifadə etməlidir.  

✅ **Nümunə:** Rate Limiting  

```csharp
services.AddRateLimiter(options =>
{
    options.GlobalLimiter = PartitionedRateLimiter.Create<HttpContext, string>(context =>
    {
        return RateLimitPartition.GetFixedWindowLimiter(
            partitionKey: context.Connection.RemoteIpAddress?.ToString() ?? "global",
            factory: _ => new FixedWindowRateLimiterOptions
            {
                PermitLimit = 100,
                Window = TimeSpan.FromMinutes(1)
            });
    });
});

app.UseRateLimiter();
```

---

## 6. Xəta İdarəetməsi və Fallback Strategiyaları  

Mikroservislər xəta verdikdə API Gateway-in alternativ bir cavab qaytarması lazım ola bilər.  

✅ **Nümunə:** Fallback ilə Alternativ Cavab  

```csharp
var fallbackPolicy = Policy<string>
    .Handle<Exception>()
    .FallbackAsync(
        fallbackValue: "Xidmət müvəqqəti olaraq əlçatmazdır.",
        onFallbackAsync: async (exception, context) =>
        {
            Console.WriteLine($"Fallback işə düşdü: {exception.Message}");
            await Task.CompletedTask;
        });

var result = await fallbackPolicy.ExecuteAsync(async () =>
{
    throw new Exception("Mikroservis xətası!");
});

Console.WriteLine($"API Gateway Cavabı: {result}");
```

---

## 7. Monitorinq və Loglama  

API Gateway-də bütün sorğuları və xəta hallarını izləyərək sistemin dayanıqlılığını artıra bilərsiniz.  

✅ **Nümunə:** OpenTelemetry ilə Monitorinq  

```csharp
using var tracer = new ActivitySource("APIGateway");

using var activity = tracer.StartActivity("ProcessRequest");
activity?.AddTag("Route", "/api/data");
activity?.AddTag("Method", "GET");

await ForwardRequestToMicroserviceAsync();
```