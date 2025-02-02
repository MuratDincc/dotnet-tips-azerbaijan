# Loqlama və Monitorinq

Loqlama və monitorinq, tətbiqinizin vəziyyəti haqqında məlumat toplamaq, problemləri aşkar etmək və performans optimizasiyası etmək üçün kritik əhəmiyyətə malikdir. Yanlış tətbiqlər loq qarışıqlığına və ya yetərsiz izləməyə yol aça bilər.

---

## 1. Yetərsiz Loq Səviyyələri

❌ **Yanlış İstifadə:** Bütün loq mesajlarını eyni səviyyədə qeyd etmək.

```csharp
logger.LogError("Tətbiq başladılır.");
logger.LogError("Bir səhv baş verdi.");
logger.LogError("Əlaqə quruldu.");
```

✅ **Düzgün İstifadə:** Loq səviyyələrini hadisənin əhəmiyyət dərəcəsinə görə tənzimləyin.

```csharp
logger.LogInformation("Tətbiq başladılır.");
logger.LogError("Bir səhv baş verdi.");
logger.LogDebug("Əlaqə quruldu.");
```

---

## 2. Səhv Loqlama Forması

❌ **Yanlış İstifadə:** Loq mesajlarını oxunması anlaşıqsız formatlarda qeyd etmək.

```csharp
logger.LogInformation("Error123: ModulX StepY-də uğursuz oldu.");
```

✅ **Düzgün İstifadə:** Strukturlaşdırılmış loqlama istifadə edərək oxunaqlı və analiz edilə bilən loq formatları yaradın.

```csharp
logger.LogInformation("Modul {Module} {Step} addımında uğursuz oldu.", "X", "Y");
```

---

## 3. Loqların Gərəksiz Detal Ehtiva Etməsi

❌ **Yanlış İstifadə:** Hər detalı loqlamaq.

```csharp
logger.LogInformation("İstifadəçi ID: 123, Ad: Murad Dinç, IP: 192.168.1.1, Brauzer: Chrome...");
```

✅ **Düzgün İstifadə:** Vacib məlumatları qeyd edin, həssas məlumatları xaric edin.

```csharp
logger.LogInformation("İstifadəçi ID: {UserId} daxil oldu.", 123);
```

---

## 4. Loq Rotation İstifadə Edilməməsi

❌ **Yanlış İstifadə:** Loq fayllarını döndərmədən daimi böyütmək.

```plaintext
app.log // Sonsuza qədər böyüyən loq faylı
```

✅ **Düzgün İstifadə:** Loq rotation xüsusiyyətini aktivləşdirin.

- **Nümunə:** Serilog ilə loq fayllarını döndərmək.

```csharp
Log.Logger = new LoggerConfiguration()
    .WriteTo.File("logs/log-.txt", rollingInterval: RollingInterval.Day)
    .CreateLogger();
```

---

## 5. Mərkəzi Loq İdarəetməsi Əskikliyi

❌ **Yanlış İstifadə:** Loqların yalnız lokal olaraq saxlanması.

```plaintext
local-logs/app.log
```

✅ **Düzgün İstifadə:** Mərkəzi bir loq idarəetmə sistemi istifadə edərək loqları bir araya gətirin.

- **Azure Application Insights**
- **Elastic Stack (ELK)**
- **Datadog**

```csharp
services.AddApplicationInsightsTelemetry();
```

---

## 6. Monitorinqin Əskikliyi

❌ **Yanlış İstifadə:** Tətbiqin performansını və vəziyyətini izləməmək.

✅ **Düzgün İstifadə:** Performans və səhv izləmə alətlərini inteqrasiya edin.

- **Prometheus və Grafana**
- **Azure Monitor**
- **New Relic**

---

## 7. Səhv Loqlama Tezliyi

❌ **Yanlış İstifadə:** Tez-tez baş verən hadisələri həddindən artıq loqlamaq.

```csharp
for (int i = 0; i < 10000; i++)
{
    logger.LogInformation("Əməliyyat tamamlandı.");
}
```

✅ **Düzgün İstifadə:** Vacib hadisələri loqlayın və loqlama tezliyini yoxlayın.

```csharp
if (transactionCount % 100 == 0)
{
    logger.LogInformation("{TransactionCount} əməliyyat tamamlandı.", transactionCount);
}
```

---

## 8. Loqlarda Həssas Məlumatların Saxlanması

❌ **Yanlış İstifadə:** Şifrələri və ya həssas məlumatları loqlara daxil etmək.

```csharp
logger.LogInformation("İstifadəçi şifrəsi: {Password}", "123456");
```

✅ **Düzgün İstifadə:** Həssas məlumatları xaric edin.

```csharp
logger.LogInformation("İstifadəçi daxil oldu: {UserId}", userId);
```
