# Idempotency Key və Məlumatın Tutarlılığı  

Idempotency, bir əməliyyatın bir neçə dəfə çağırıldığında eyni nəticəni təmin etməsi deməkdir. Bu xüsusiyyət, paylanmış sistemlərdə məlumatın tutarlılığını qorumaq və təkrarlanan əməliyyatların qarşısını almaq üçün vacib bir strategiyadır.  

---

## 1. Idempotency Nədir?  

- **Məqsəd:** Eyni əməliyyat bir neçə dəfə təkrarlandıqda sistemin vəziyyətini dəyişdirmədən eyni nəticəni qaytarması.  
- **İstifadə Sahələri:** API çağırışları, ödəniş sistemləri, mesajlaşma sistemləri.  

Məsələn, bir ödəniş əməliyyatı eyni API çağırışı təkrarlandığı üçün bir neçə dəfə icra edilməməlidir.  

---

## 2. Idempotency Key İstifadəsi  

Idempotency Key, bir sorğunu unikal şəkildə tanıdan açardır. Bu açar, müştəridən gələn sorğunun təkrar emal edilməsinin qarşısını alır.  

❌ **Yanlış İstifadə:** Idempotency Key olmadan sorğunu emal etmək  

```csharp
public async Task ProcessPaymentAsync(PaymentRequest request)
{
    // Təkrarlanan sorğuların yoxlanılmadan emal edilməsi
    await SavePaymentToDatabaseAsync(request);
    await ChargeCustomerAsync(request);
}
```

✅ **Düzgün İstifadə:** Idempotency Key ilə yoxlama  

```csharp
public async Task ProcessPaymentAsync(PaymentRequest request)
{
    if (await IsRequestAlreadyProcessedAsync(request.IdempotencyKey))
    {
        Console.WriteLine("Bu sorğu artıq emal edilib. Keçilir.");
        return;
    }

    await SavePaymentToDatabaseAsync(request);
    await ChargeCustomerAsync(request);
    await MarkRequestAsProcessedAsync(request.IdempotencyKey);
}
```

---

## 3. Idempotency Key və Verilənlər Bazası İstifadəsi  

Idempotency Key adətən verilənlər bazasında saxlanılır və hər bir sorğunun unikal olduğunu təmin edir.  

✅ **Nümunə:** Verilənlər bazasında Idempotency Key yoxlaması  

```csharp
public async Task<bool> IsRequestAlreadyProcessedAsync(string idempotencyKey)
{
    return await _dbContext.IdempotencyKeys.AnyAsync(k => k.Key == idempotencyKey);
}

public async Task MarkRequestAsProcessedAsync(string idempotencyKey)
{
    await _dbContext.IdempotencyKeys.AddAsync(new IdempotencyKey { Key = idempotencyKey });
    await _dbContext.SaveChangesAsync();
}
```

---

## 4. Mesajlaşma Sistemlərində Idempotency  

Mesajlar bir neçə dəfə emal edilə bilər, buna görə də idempotent dizayn istifadə etmək vacibdir.  

✅ **Nümunə:** Mesajın emalı  

```csharp
public async Task ProcessMessageAsync(Message message)
{
    if (await IsMessageAlreadyProcessedAsync(message.Id))
    {
        Console.WriteLine("Bu mesaj artıq emal edilib.");
        return;
    }

    await HandleMessageAsync(message);
    await MarkMessageAsProcessedAsync(message.Id);
}
```

---

## 5. Idempotency və API Dizaynı  

REST API-lərdə idempotency, müştərilərin etibarlı şəkildə məlumat göndərməsini və qəbul etməsini təmin edir.  

✅ **Nümunə:** Idempotency Key ilə API Çağırışı  

```http
POST /payments HTTP/1.1
Content-Type: application/json
Idempotency-Key: 12345

{
    "amount": 100,
    "currency": "USD"
}
```

Server tərəfində:  

```csharp
if (await IsRequestAlreadyProcessedAsync(request.Headers["Idempotency-Key"]))
{
    return Ok("Bu sorğu artıq emal edilib.");
}
```

---

## 6. Loglama və Monitorinq  

Idempotency sistemlərinin düzgün işlədiyini təmin etmək üçün loglama və monitorinq vasitələrindən istifadə edin.  

✅ **Nümunə:** Loglama ilə Idempotency İzləmə  

```csharp
public async Task ProcessPaymentAsync(PaymentRequest request)
{
    if (await IsRequestAlreadyProcessedAsync(request.IdempotencyKey))
    {
        Console.WriteLine($"Təkrarlanan sorğu aşkar edildi: {request.IdempotencyKey}");
        return;
    }

    await SavePaymentToDatabaseAsync(request);
    Console.WriteLine($"Sorğu emal edildi: {request.IdempotencyKey}");
    await MarkRequestAsProcessedAsync(request.IdempotencyKey);
}
```