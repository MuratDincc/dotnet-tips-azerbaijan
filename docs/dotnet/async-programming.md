# Asinxron Proqramlaşdırma və Task İdarəetməsi

Asinxron proqramlaşdırma, müasir tətbiqlərdə performans və cavab vermə qabiliyyəti baxımından böyük əhəmiyyət daşıyır. Lakin, yanlış istifadə edildikdə gözlənilməz problemlərə yol aça bilər. Budur C#'da asinxron proqramlaşdırma üçün tez-tez edilən səhvlər və təklif olunan həllər.

---

## 1. `await` İstifadəsini Atlatmaq

❌ **Yanlış İstifadə:** `await` istifadə edilmədikdə istisna vəziyyətləri düzgün şəkildə idarə oluna bilməz.

```csharp
try
{
    DoWorkWithoutAwaitAsync();
}
catch (Exception e)
{
    Console.WriteLine($"Xəta: {e.Message}");
}

static Task DoWorkWithoutAwaitAsync()
{
    return ThrowExceptionAsync();
}

static async Task ThrowExceptionAsync()
{
    await Task.Yield();
    throw new Exception("Bir xəta baş verdi!");
}
```

✅ **Düzgün İstifadə:** `await` ilə çağırışı gözləyərək xəta idarəetməsini yaxşılaşdırın.

```csharp
try
{
    await DoWorkWithAwaitAsync();
}
catch (Exception e)
{
    Console.WriteLine($"Xəta: {e.Message}");
}

static async Task DoWorkWithAwaitAsync()
{
    await ThrowExceptionAsync();
}

static async Task ThrowExceptionAsync()
{
    await Task.Yield();
    throw new Exception("Bir xəta baş verdi!");
}
```

---

## 2. `async void` İstifadəsi

❌ **Yanlış İstifadə:** `async void` xətaları düzgün bir şəkildə idarə etməyi çətinləşdirir.

```csharp
public async void DoAsync()
{
    await SomeAsyncOperation();
}
```

✅ **Düzgün İstifadə:** `async Task` istifadə edərək xəta idarəetməsini və test edilə bilərliyi artırın.

```csharp
public async Task DoAsync()
{
    await SomeAsyncOperation();
}
```

---

## 3. `Task` Obyektini Gözləmədən Döndərmək

❌ **Yanlış İstifadə:** `using` bloku içində `Task` obyektini gözləmədən döndərmək resursların erkən sərbəst buraxılmasına səbəb ola bilər.

```csharp
public Task<string> GetContentAsync()
{
    using var client = new HttpClient();
    return client.GetStringAsync("https://example.com");
}
```

✅ **Düzgün İstifadə:** `await` istifadə edərək resurs idarəetməsini maksimallaşdırın.

```csharp
public async Task<string> GetContentAsync()
{
    using var client = new HttpClient();
    return await client.GetStringAsync("https://example.com");
}
```

---

## 4. Paralel Əməliyyatların Yanlış İdarəetməsi

❌ **Yanlış İstifadə:** Paralel tapşırıqları ardıcıllıqla gözləmək.

```csharp
await Task1();
await Task2();
```

✅ **Düzgün İstifadə:** Tapşırıqları paralel olaraq başladın və eyni anda gözləyin.

```csharp
var task1 = Task1();
var task2 = Task2();
await Task.WhenAll(task1, task2);
```

---

## 5. `Task.Delay` ilə Həssas Zamanlama

❌ **Yanlış İstifadə:** Qısa müddətli həssas zamanlama üçün `Task.Delay` istifadə etmək.

```csharp
await Task.Delay(1); // Müddət tam olaraq 1ms olmaya bilər.
```

✅ **Düzgün İstifadə:** Həssas zamanlama üçün daha uyğun alətlər istifadə edin.

---

## 6. Deadlock Problemləri

❌ **Yanlış İstifadə:** `Task.Result` və ya `Task.Wait` istifadə edərək deadlock yaratmaq.

```csharp
var result = SomeAsyncOperation().Result;
```

✅ **Düzgün İstifadə:** `await` istifadə edərək deadlock problemlərinin qarşısını alın.

```csharp
var result = await SomeAsyncOperation();
```

---

## 7. `Task` Obyektlərini Geri Dönüştürmək

❌ **Yanlış İstifadə:** Hər əməliyyat üçün yeni bir `Task` obyekti yaratmaq.

```csharp
public Task<int> GetNumberAsync()
{
    return Task.Run(() => 42);
}
```

✅ **Düzgün İstifadə:** `Task.FromResult` istifadə edərək lazımsız obyekt yaratmağın qarşısını alın.

```csharp
public Task<int> GetNumberAsync()
{
    return Task.FromResult(42);
}
```
