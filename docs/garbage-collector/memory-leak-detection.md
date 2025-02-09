# Garbage Collector: Yaddaş Sızması Aşkarlanması

Yaddaş sızmaları, bir tətbiqin gərəyindən çox yaddaş istehlak etməsinə və zamanla performans düşüşünə səbəb ola bilər. .NET-in Garbage Collector (GC) mexanizmi ümumiyyətlə yaddaş idarəsini avtomatik olaraq edir, lakin yanlış referans idarəetməsi və ya mürəkkəb obyekt əlaqələri yaddaş sızmalarına yol aça bilər.

---

## 1. Yaddaş Sızması Nədir?

Yaddaş sızması, artıq istifadə olunmayan, lakin Garbage Collector tərəfindən sərbəst buraxılmayan obyektlərin yaddaş istehlak etməyə davam etməsi vəziyyətidir. Bu, ümumiyyətlə aşağıdakı səbəblərdən qaynaqlanır:

- **Yanlış Referans İdarəetməsi:** Gərəksiz referansların tutulması.
- **Hadisə (Event) Abunəlikləri:** `event` abunəliklərinin ləğv edilməməsi.
- **Statik Obyektlər:** Statik sahələrdə gərəksiz verilən tutulması.

---

## 2. Yaddaş Sızması Necə Aşkarlanır?

.NET tətbiqlərində yaddaş sızmalarını aşkar etmək üçün bu vasitələri istifadə edə bilərsiniz:

### Visual Studio Diagnostic Tools
- **Memory Usage:** Tətbiqin yaddaş istifadəsini izləyir.
- **Heap Snapshots:** Ani yaddaş vəziyyətlərini qarşılaşdırır.

### .NET CLI Tools
- **dotnet-dump:** Yaddaş dökümləri yaradır və analiz edir.
- **dotnet-counters:** Gerçək zamanlı yaddaş ölçümləri təmin edir.

```bash
dotnet-dump collect --process-id <pid>
dotnet-counters monitor --counters Microsoft-Windows-DotNETRuntime:GC/Heap
```

---

## 3. Yaddaş Sızması Səbəbləri və Həlləri

### Yanlış Referans İdarəetməsi

❌ **Yanlış İstifadə:** Gərəksiz referansları təmizləməmək.

```csharp
static List<byte[]> cache = new();

void AddToCache()
{
    var data = new byte[1024 * 1024];
    cache.Add(data); // Referans buraxılmır
}
```

✅ **Düzgün İstifadə:** Referansları zamanında sərbəst buraxmaq.

```csharp
static List<byte[]> cache = new();

void ClearCache()
{
    cache.Clear(); // Referanslar sərbəst buraxılır
}
```

---

### Event Abunəliklərini İdarə Etməmək

❌ **Yanlış İstifadə:** Event'lərə abunə olduqdan sonra ləğv etməmək.

```csharp
button.Click += OnButtonClick; // Abunəlik ləğv edilməz
```

✅ **Düzgün İstifadə:** Event abunəliklərini ləğv edin.

```csharp
button.Click -= OnButtonClick; // Abunəlik ləğv edilir
```

---

### Statik Sahələrdə Veri Tutmaq

❌ **Yanlış İstifadə:** Statik sahələrdə böyük obyektləri gərəksiz tutmaq.

```csharp
static List<int> staticData = new() { 1, 2, 3 };
```

✅ **Düzgün İstifadə:** Statik sahələri diqqətli idarə edin.

```csharp
static WeakReference<List<int>> staticData = new(new List<int> { 1, 2, 3 });
```

---

## 4. Garbage Collector Diaqnostik Modu

.NET 9 ilə gələn diaqnostik vasitələr, Garbage Collector-nin yaddaş idarəsini analiz etməyi asanlaşdırır.

```xml
<configuration>
  <runtime>
    <GCHeapHardLimitPercent value="80" />
  </runtime>
</configuration>
```

---

## 5. Profiling və İzləmə

- **JetBrains dotMemory:** Dərin yaddaş analizi.
- **Redgate ANTS Memory Profiler:** Yaddaş sızıntılarını aşkar etmek için istifadə olunur.
