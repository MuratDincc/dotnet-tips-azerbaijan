# Arxa Plan Xidmətləri: Yanlış və Düzgün İstifadə

Arxa plan xidmətləri, uzunmüddətli əməliyyatları və ya arxa planda işləməsi lazım olan tapşırıqları idarə etmək üçün istifadə olunur. Yanlış dizayn edilmiş arxa plan xidmətləri performans problemlərinə, resurs istehlakına və gözlənilməz xətalara yol aça bilər.

---

## 1. Uzunmüddətli Əməliyyatları UI Thread-də İşlətmək

❌ **Yanlış İstifadə:** Uzunmüddətli əməliyyatları UI thread-də işlətmək.

```csharp
public void DoWork()
{
    Thread.Sleep(5000); // UI thread-i bloklayır
}
```

✅ **Düzgün İstifadə:** Uzunmüddətli əməliyyatları arxa planda işlədin.

```csharp
public class BackgroundTaskService : BackgroundService
{
    protected override async Task ExecuteAsync(CancellationToken stoppingToken)
    {
        while (!stoppingToken.IsCancellationRequested)
        {
            await Task.Delay(5000, stoppingToken); // Arxa planda işləyir
        }
    }
}
```

---

## 2. CancellationToken İstifadəsinə Etinasız yanaşmaq

❌ **Yanlış İstifadə:** CancellationToken-i olmadan arxa plan tapşırıqları yaratmaq.

```csharp
protected override async Task ExecuteAsync(CancellationToken stoppingToken)
{
    while (true)
    {
        await Task.Delay(1000); // İmtina edilə bilməz
    }
}
```

✅ **Düzgün İstifadə:** `CancellationToken` istifadə edərək tapşırıqların düzgün şəkildə imtina edilməsini təmin edin.

```csharp
protected override async Task ExecuteAsync(CancellationToken stoppingToken)
{
    while (!stoppingToken.IsCancellationRequested)
    {
        await Task.Delay(1000, stoppingToken);
    }
}
```

---

## 3. Xətaları Nəzarətsiz Buraxmaq

❌ **Yanlış İstifadə:** Arxa plan tapşırıqlarındakı xətaları göz ardı etmək.

```csharp
protected override async Task ExecuteAsync(CancellationToken stoppingToken)
{
    while (!stoppingToken.IsCancellationRequested)
    {
        throw new Exception("Bir xəta baş verdi."); // İdarə olunmaz
    }
}
```

✅ **Düzgün İstifadə:** Xətaları tutaraq loqlayın və idarə edilə bilər hala gətirin.

```csharp
protected override async Task ExecuteAsync(CancellationToken stoppingToken)
{
    while (!stoppingToken.IsCancellationRequested)
    {
        try
        {
            // İş məntiqi
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Xəta: {ex.Message}");
        }
    }
}
```

---

## 4. Həddindən Artıq Sayda Background Service Yaratmaq

❌ **Yanlış İstifadə:** Hər tapşırıq üçün ayrı bir background service təyin etmək.

```plaintext
services.AddHostedService<Service1>();
services.AddHostedService<Service2>();
services.AddHostedService<Service3>();
```

✅ **Düzgün İstifadə:** Tapşırıqları birləşdirərək idarə oluna bilər bir quruluş yaradın.

```csharp
public class CombinedBackgroundService : BackgroundService
{
    protected override async Task ExecuteAsync(CancellationToken stoppingToken)
    {
        var task1 = Task1(stoppingToken);
        var task2 = Task2(stoppingToken);
        await Task.WhenAll(task1, task2);
    }

    private async Task Task1(CancellationToken stoppingToken) { /* ... */ }
    private async Task Task2(CancellationToken stoppingToken) { /* ... */ }
}
```

---

## 5. Resursları Sərbəst Buraxmamaq

❌ **Yanlış İstifadə:** Arxa plan tapşırıqlarında istifadə edilən resursları təmizləməmək.

```csharp
public class MyBackgroundService : BackgroundService
{
    private readonly Timer _timer = new Timer(OnTimerElapsed);

    protected override Task ExecuteAsync(CancellationToken stoppingToken)
    {
        _timer.Change(0, 1000); // Timer başladılır, ancaq təmizlənmir
        return Task.CompletedTask;
    }
}
```

✅ **Düzgün İstifadə:** `Dispose` metodunu istifadə edərək resursları təmizləyin.

```csharp
public class MyBackgroundService : BackgroundService, IDisposable
{
    private readonly Timer _timer = new Timer(OnTimerElapsed);

    protected override Task ExecuteAsync(CancellationToken stoppingToken)
    {
        _timer.Change(0, 1000);
        return Task.CompletedTask;
    }

    public override void Dispose()
    {
        _timer?.Dispose();
        base.Dispose();
    }
}
```

---

## 6. Performansı İzləməmək

❌ **Yanlış İstifadə:** Arxa plan tapşırıqlarının performansını və vəziyyətini izləməmək.

✅ **Düzgün İstifadə:** İzləmə alətləri istifadə edərək performansı ölçün.

- **Nümunə Alətlər:** Application Insights, Prometheus, Grafana

```csharp
protected override async Task ExecuteAsync(CancellationToken stoppingToken)
{
    var stopwatch = Stopwatch.StartNew();

    while (!stoppingToken.IsCancellationRequested)
    {
        stopwatch.Restart();
        await DoWorkAsync();
        Console.WriteLine($"Tapşırıq müddəti: {stopwatch.ElapsedMilliseconds} ms");
    }
}
```
