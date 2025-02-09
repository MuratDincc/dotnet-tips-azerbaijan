# Garbage Collector: .NET 9-dakı Yeni Xüsusiyyətlər

.NET 9, Garbage Collector (GC) üzərində müxtəlif təkmilləşdirmələr və yeni xüsusiyyətlər təqdim edərək yaddaş idarəetmə performansını daha da inkişaf etdirmişdir. Bu xüsusiyyətlər, böyük miqyaslı tətbiqlərdə daha yaxşı səmərəlilik və daha aşağı gecikmə müddətləri təmin etməyi hədəfləyir.

---

## 1. Dynamic PGO ilə Yaddaş İdarəetməsi

**Dynamic Profile-Guided Optimization (Dynamic PGO)**, GC-nin icra zamanı tətbiqinizin davranışına uyğun optimallaşmasına imkan verir.

✅ **Üstünlükləri:**
- Real vaxtda optimallaşdırma.
- Daha yaxşı yaddaş ayırması.
- Performansın artması.

```csharp
// Dynamic PGO-nun avtomatik aktiv olduğu bir tətbiq nümunəsi
void PerformTask()
{
    var data = new List<int>();
    for (int i = 0; i < 1000; i++)
    {
        data.Add(i);
    }
    // GC daha səmərəli ayırma və toplanma həyata keçirir.
}
```

---

## 2. LOH (Large Object Heap) Sıxlaşdırma

.NET 9, Large Object Heap (LOH) üçün sıxlaşdırma funksionallığını əlavə edərək böyük obyektlərin daha effektiv idarə olunmasını təmin edir.

❌ **Əvvəlki Davranış:**
LOH üzərində böyük obyektlər sıxlaşdırılmadan saxlanılırdı, bu isə yaddaşın parçalanmasına səbəb olurdu.

✅ **Yeni Xüsusiyyət:**
LOH sıxlaşdırması, yaddaşın parçalanmasını azaldır.

```xml
<configuration>
  <runtime>
    <GCLOHCompact enabled="true" />
  </runtime>
</configuration>
```

---

## 3. GC-nin Daha Yaxşı Thread İdarəetməsi

.NET 9, Garbage Collector-ın thread idarəetmə alqoritmlərində təkmilləşdirmələr etmişdir. Bu yeniliklər, xüsusilə çox iş parçacığı istifadə edən server tətbiqlərində daha aşağı gecikmə təmin edir.

```csharp
<configuration>
  <runtime>
    <gcServer enabled="true" />
  </runtime>
</configuration>
```

---

## 4. Regional Yaddaş İdarəetməsi (Regional GC)

.NET 9, GC-nin yaddaş idarəetməsini daha regional hala gətirərək yaddaş ayırma prosesini sürətləndirir və gecikmə müddətini azaldır.

✅ **Üstünlükləri:**
- Daha kiçik yaddaş blokları üzərində işləmə.
- Yaddaş ayırma vaxtının azaldılması.

---

## 5. Daha Yaxşı Diaqnostika və İzləmə Alətləri

.NET 9, Garbage Collector performansını izləmək üçün təkmilləşdirilmiş diaqnostika alətləri təqdim edir. Bu alətlər vasitəsilə GC-nin necə işlədiyini daha detallı şəkildə anlaya bilərsiniz.

```bash
dotnet-counters monitor --process-id <pid> --counters Microsoft-Windows-DotNETRuntime:GC/Heap
```

---

## 6. GC Performans Modu Seçimləri

.NET 9, tətbiqin ehtiyaclarına uyğun fərqli GC modları təklif edir:

- **Interactive Mode:** İstifadəçi yönümlü tətbiqlər üçün aşağı gecikmə təmin edir.
- **Batch Mode:** Server yönümlü tətbiqlərdə daha yüksək throughput üçün optimallaşdırılmışdır.

```xml
<configuration>
  <runtime>
    <GCLatencyMode value="Interactive" />
  </runtime>
</configuration>
```

---

## 7. Təkmilləşdirilmiş İş Parçacığı Optimizasiyası

.NET 9, GC-nin iş parçacıq idarəetməsini daha effektiv hala gətirmək üçün inkişaf etmiş alqoritmlər təqdim edir. Bu, xüsusilə çoxnüvəli prosessorlarda performansın artmasını təmin edir.

```csharp
ThreadPool.SetMinThreads(10, 10);
```