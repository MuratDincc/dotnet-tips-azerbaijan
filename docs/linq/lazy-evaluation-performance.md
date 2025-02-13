# LINQ: Lazy Evaluation və Performansa Təsiri

LINQ sorğularının necə işlədiyini anlamaq, həm performansı optimallaşdırmaq, həm də gözlənilməz nəticələrin qarşısını almaq üçün vacibdir. **LINQ sorğuları standart olaraq "lazy evaluation" (təxirə salınmış icra) prinsipi ilə işləyir**.

---

## 1. Lazy Evaluation Nədir?

Lazy evaluation, bir LINQ sorğusunun ancaq nəticə tələb edildikdə işlədilməsidir. Bu metod lazımsız sorğuların qarşısını alır və yaddaş istifadəsini azaldır.

✅ **Misal:**

```csharp
var query = numbers.Where(n => n > 10);

// Sorğu burada işləmir, sadəcə tərif edilir.
foreach (var number in query)
{
    Console.WriteLine(number);
}
// Sorğu yalnız burada işləyəcək.
```

Bu nümunədə `query` sadəcə təyin edilir, ancaq **foreach** işlədikdə verilənlər bazasına sorğu göndərilir.

---

## 2. Səhv və Düzgün İstifadə

### **Səhv İstifadə:** Lazımsız sorğu zəncirləri yaratmaq

❌ **Səhv İstifadə:**

```csharp
var query = numbers.Where(n => n > 10).OrderBy(n => n).Skip(5);
// Sorğu lazımsız şəkildə işlənir və performansa təsir edir
var result = query.ToList();
```

Bu metod hər bir addımı lazımsız işləyərək verilənlər bazasında ağır bir sorğuya çevrilə bilər.

---

### **Düzgün İstifadə:** Yalnız ehtiyac duyulan əməliyyatları yerinə yetirmək

✅ **Düzgün İstifadə:**

```csharp
var result = numbers
    .Where(n => n > 10)
    .OrderBy(n => n)
    .Skip(5)
    .Take(10)
    .ToList();
```

Bu yanaşma yalnız ehtiyac duyulan məlumatı işləyərək performansı artırır.

---

## 3. Immediate Execution (Dərhal İcra)

Əgər bir sorğunun **dərhal işlənməsini** istəyirsinizsə, `ToList()`, `Count()`, və ya `First()` kimi metodlardan istifadə edə bilərsiniz.

✅ **Misal:**

```csharp
var result = numbers.Where(n => n > 10).ToList();
// Sorğu burada dərhal işləyəcək.
```

**Immediate execution**, xüsusilə nəticələrin bir neçə dəfə istifadə ediləcəyi hallarda faydalıdır.

---

## 4. Performans Problemi: Multiple Iteration (Təkrarlanan İşləmə)

Bir LINQ sorğusu **bir neçə dəfə icraya məruz qalarsa**, hər dəfə yenidən işlənəcək.

❌ **Səhv İstifadə:**

```csharp
var query = numbers.Where(n => n > 10);

Console.WriteLine(query.Count()); // Sorğu işləyir
foreach (var number in query) // Sorğu yenidən işləyir
{
    Console.WriteLine(number);
}
```

Bu yanaşma eyni sorğunun **iki dəfə icra edilməsinə** səbəb olur.

✅ **Düzgün İstifadə:**

```csharp
var result = numbers.Where(n => n > 10).ToList();

Console.WriteLine(result.Count());
foreach (var number in result)
{
    Console.WriteLine(number);
}
```

Bu metod, **sorğunun yalnız bir dəfə işlənməsini təmin edir**.

---

## 5. Performans Testi

Lazy evaluation ilə immediate execution arasındakı fərqi ölçmək üçün:

✅ **Performans Testi Nümunəsi:**

```csharp
var stopwatch = Stopwatch.StartNew();

// Lazy Evaluation
var query = numbers.Where(n => n > 10);
stopwatch.Stop();
Console.WriteLine($"Lazy Evaluation Vaxtı: {stopwatch.ElapsedMilliseconds} ms");

stopwatch.Restart();

// Immediate Execution
var result = numbers.Where(n => n > 10).ToList();
stopwatch.Stop();
Console.WriteLine($"Immediate Execution Vaxtı: {stopwatch.ElapsedMilliseconds} ms");
```

---

## 6. Lazy Evaluation-un Üstünlükləri və Çatışmazlıqları

| **Üstünlüklər**                                       | **Çatışmazlıqlar**                                   |
|------------------------------------------------------|---------------------------------------------------|
| Yaddaş istifadəsini optimallaşdırır                   | Təkrarlanan iterasiya performansı aşağı sala bilər |
| Sorğular yalnız ehtiyac olduqda işlənir               | Kompleks sorğu zəncirləri ağır verilənlər bazası sorğuları yarada bilər |
| Məlumat tam yüklənmədən emal edilə bilər              | Sorğuların gözlənilməz vaxtlarda işləməsinə səbəb ola bilər |
