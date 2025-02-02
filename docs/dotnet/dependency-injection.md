# Dependency Injection (DI)

Dependency Injection (DI), bir tətbiqdə asılılıqların idarə edilməsini asanlaşdıraraq test edilə bilərliyi və kodun davamlılığını artırır. Lakin DI yanlış istifadə edildikdə kodun mürəkkəbliyini artıra bilər.

---

## 1. Asılılıqların Əllə İdarə Edilməsi

❌ **Yanlış İstifadə:** Asılılıqları əllə nümunələndirmək.

```csharp
public class OrderService
{
    private readonly ProductRepository _productRepository;

    public OrderService()
    {
        _productRepository = new ProductRepository(); // Bərk asılılıq
    }
}
```

✅ **Düzgün İstifadə:** Asılılıqları bir IoC konteyneri üzərindən injektə etmək.

```csharp
public class OrderService
{
    private readonly IProductRepository _productRepository;

    public OrderService(IProductRepository productRepository)
    {
        _productRepository = productRepository;
    }
}
```

---

## 2. Yanlış Yaşam Müddəti (Lifetime) İdarəetməsi

❌ **Yanlış İstifadə:** Scoped asılılıqları singleton bir xidmətə injektə etmək.

```csharp
services.AddSingleton<MyService>();
services.AddScoped<MyDbContext>();

public class MyService
{
    private readonly MyDbContext _dbContext;

    public MyService(MyDbContext dbContext)
    {
        _dbContext = dbContext; // Səhv yaşam müddəti
    }
}
```

✅ **Düzgün İstifadə:** Asılılıqların yaşam müddətini düzgün konfiqurasiya edin.

```csharp
services.AddScoped<MyService>();
services.AddScoped<MyDbContext>();

public class MyService
{
    private readonly MyDbContext _dbContext;

    public MyService(MyDbContext dbContext)
    {
        _dbContext = dbContext;
    }
}
```

---

## 3. Çox Asılılıq İnjektə Etmək

❌ **Yanlış İstifadə:** Bir sinifdə çox asılılığı birbaşa injektə etmək.

```csharp
public class MyController
{
    public MyController(IService1 service1, IService2 service2, IService3 service3, IService4 service4)
    {
        // Çox fazla asılılıq
    }
}
```

✅ **Düzgün İstifadə:** Asılılıqları qruplaşdıraraq bir interfeys ilə abstraktlaşdırın.

```csharp
public interface IServiceGroup
{
    IService1 Service1 { get; }
    IService2 Service2 { get; }
}

public class ServiceGroup : IServiceGroup
{
    public IService1 Service1 { get; }
    public IService2 Service2 { get; }

    public ServiceGroup(IService1 service1, IService2 service2)
    {
        Service1 = service1;
        Service2 = service2;
    }
}
```

---

## 4. Service Locator İstifadəsi

❌ **Yanlış İstifadə:** Service locator anti-pattern’ini istifadə etmək.

```csharp
public class MyService
{
    public void DoWork()
    {
        var service = ServiceLocator.GetService<IMyDependency>();
        service.PerformAction();
    }
}
```

✅ **Düzgün İstifadə:** Asılılıqları birbaşa injektə edin.

```csharp
public class MyService
{
    private readonly IMyDependency _myDependency;

    public MyService(IMyDependency myDependency)
    {
        _myDependency = myDependency;
    }

    public void DoWork()
    {
        _myDependency.PerformAction();
    }
}
```

---

## 5. Test Edilə Bilərliyi Göz Ardı Etmək

❌ **Yanlış İstifadə:** Güclü asılılıqlar səbəbindən test edilə bilməyən siniflər.

```csharp
public class ReportService
{
    private readonly Logger _logger = new Logger();

    public void GenerateReport()
    {
        _logger.Log("Hesabat yaradılır...");
    }
}
```

✅ **Düzgün İstifadə:** Logger kimi asılılıqları injectə edərək test edilə bilərliyi artırın.

```csharp
public class ReportService
{
    private readonly ILogger _logger;

    public ReportService(ILogger logger)
    {
        _logger = logger;
    }

    public void GenerateReport()
    {
        _logger.Log("Hesabat yaradılır...");
    }
}
```

---

## 6. Xidmətlərin Həddindən Artıq Registrasiyası

❌ **Yanlış İstifadə:** Hər asılılığı əllə qeyd etmək.

```csharp
services.AddSingleton<IService1, Service1>();
services.AddSingleton<IService2, Service2>();
services.AddSingleton<IService3, Service3>();
```

✅ **Düzgün İstifadə:** Assembly skan etmə ilə xidmətləri avtomatik olaraq qeyd edin.

```csharp
var assemblies = AppDomain.CurrentDomain.GetAssemblies();
services.Scan(scan => scan
    .FromAssemblies(assemblies)
    .AddClasses()
    .AsImplementedInterfaces()
    .WithScopedLifetime());
```
