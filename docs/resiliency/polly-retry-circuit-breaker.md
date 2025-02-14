# Polly ilə Retry və Circuit Breaker  

Polly, dayanıqlı və etibarlı tətbiqlər yaratmaq üçün istifadə olunan güclü bir .NET kitabxanasıdır. Şəbəkə xətalarına, müvəqqəti problemlərə və ya zaman aşımı xətalarına qarşı strategiyalar təqdim edərək tətbiqinizin daha sabit işləməsini təmin edir.  

---

## 1. Retry (Təkrar Cəhd) Siyasəti  

Retry siyasəti, müəyyən xəta növlərində əməliyyatın yenidən icra edilməsinə imkan verir.  

❌ **Yanlış İstifadə:** Müəyyən bir siyasət təyin etmədən yenidən cəhd etmək.  

```csharp
for (int i = 0; i < 3; i++)
{
    try
    {
        await MakeHttpRequestAsync();
        break;
    }
    catch
    {
        if (i == 2) throw;
    }
}
```

✅ **Düzgün İstifadə:** Polly ilə təkrar cəhd strategiyası təyin edin.  

```csharp
var retryPolicy = Policy
    .Handle<HttpRequestException>()
    .WaitAndRetryAsync(3, retryAttempt => TimeSpan.FromSeconds(Math.Pow(2, retryAttempt)));

await retryPolicy.ExecuteAsync(() => MakeHttpRequestAsync());
```

---

## 2. Circuit Breaker (Devre Kəsici) Siyasəti  

Circuit Breaker, müəyyən sayda xəta baş verdikdən sonra müvəqqəti olaraq əməliyyatları dayandırır.  

❌ **Yanlış İstifadə:** Xətaları nəzarətsiz şəkildə toplamaq.  

```csharp
int failureCount = 0;

try
{
    await MakeHttpRequestAsync();
}
catch
{
    failureCount++;
    if (failureCount > 5)
    {
        throw new Exception("Çox sayda uğursuz cəhd!");
    }
}
```

✅ **Düzgün İstifadə:** Polly ilə Circuit Breaker strategiyasını tətbiq edin.  

```csharp
var circuitBreakerPolicy = Policy
    .Handle<HttpRequestException>()
    .CircuitBreakerAsync(5, TimeSpan.FromMinutes(1));

await circuitBreakerPolicy.ExecuteAsync(() => MakeHttpRequestAsync());
```

---

## 3. Retry və Circuit Breaker Kombinasiyası  

Polly, bir neçə strategiyanı birləşdirərək daha çevik siyasətlər yaratmağa imkan verir.  

✅ **Nümunə:**  

```csharp
var retryPolicy = Policy
    .Handle<HttpRequestException>()
    .WaitAndRetryAsync(3, retryAttempt => TimeSpan.FromSeconds(retryAttempt));

var circuitBreakerPolicy = Policy
    .Handle<HttpRequestException>()
    .CircuitBreakerAsync(2, TimeSpan.FromSeconds(30));

var combinedPolicy = Policy.WrapAsync(retryPolicy, circuitBreakerPolicy);

await combinedPolicy.ExecuteAsync(() => MakeHttpRequestAsync());
```

---

## 4. Performans və Monitorinq  

- **Loglama:** Polly siyasətlərindən istifadə edərkən loglama edərək uğursuzluq və uğurlu nəticələri izləyin.  
- **Metriklər:** Uğursuzluq nisbətlərini və devre kəsici vəziyyətlərini ölçmək üçün metriklər əlavə edin.  

```csharp
var retryPolicy = Policy
    .Handle<HttpRequestException>()
    .WaitAndRetryAsync(3, retryAttempt =>
    {
        Console.WriteLine($"Retry cəhdi: {retryAttempt}");
        return TimeSpan.FromSeconds(retryAttempt);
    });
```