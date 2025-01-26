# Default Interface Methods

Default interface methods, C# 8.0 ilə təqdim olunan bir xüsusiyyətdir və interfeyslərə standart metod təyinatları əlavə etməyə imkan verir. Bu, interfeyslərin geriyə uyğunluğunu artırır, lakin düzgün istifadə edilmədikdə kodu mürəkkəbləşdirə və baxımını çətinləşdirə bilər.

---

## 1. Default Metodları Gərəksiz Yerə İstifadə Etmək

❌ **Yanlış İstifadə:** Default metodları əsas sinif funksionallıqları üçün istifadə etmək.

```csharp
public interface ILogger
{
    void Log(string message);

    void LogError(string message) // Gərəksiz bir default metod
    {
        Console.WriteLine($"Error: {message}");
    }
}
```

✅ **Düzgün İstifadə:** Default metodları yalnız geriyə uyğunluq məqsədilə istifadə edin.

```csharp
public interface ILogger
{
    void Log(string message);

    void LogError(string message)
    {
        Log($"Error: {message}");
    }
}
```

---

## 2. Mürəkkəb İş Məntiqi Əlavə Etmək

❌ **Yanlış İstifadə:** Default metodlarda mürəkkəb iş məntiqi təyin etmək.

```csharp
public interface IDataProcessor
{
    void ProcessData(string data);

    void ValidateData(string data)
    {
        if (string.IsNullOrEmpty(data))
        {
            throw new ArgumentException("Data is required.");
        }
        // Daha mürəkkəb əməliyyatlar...
    }
}
```

✅ **Düzgün İstifadə:** Mürəkkəb məntiqi siniflərdə və ya ayrı prosedurlarda idarə edin.

```csharp
public interface IDataProcessor
{
    void ProcessData(string data);

    void ValidateData(string data)
    {
        if (string.IsNullOrEmpty(data))
        {
            throw new ArgumentException("Data is required.");
        }
    }
}
```

---

## 3. Default Metodlarla Interfeysi Şişirmək

❌ **Yanlış İstifadə:** Interfeyslərə həddindən artıq sayda default metod əlavə etmək.

```csharp
public interface IReportGenerator
{
    void GenerateReport();
    void ExportReport(string format) { Console.WriteLine($"Exporting in {format} format."); }
    void PrintReport() { Console.WriteLine("Printing report."); }
}
```

✅ **Düzgün İstifadə:** Interfeysi sadə saxlayın və lazım olduqda yeni interfeyslər yaradın.

```csharp
public interface IReportGenerator
{
    void GenerateReport();
    void ExportReport(string format)
    {
        Console.WriteLine($"Exporting in {format} format.");
    }
}
```

---

## 4. Default Metodların Üstünlüklərindən Tam İstifadə Etməmək

❌ **Yanlış İstifadə:** Bütün interfeys implementasiyalarında default metodlardan faydalanmamaq.

```csharp
public interface INotifier
{
    void Notify(string message);

    void NotifyAll(string[] messages)
    {
        foreach (var message in messages)
        {
            Notify(message);
        }
    }
}
```

✅ **Düzgün İstifadə:** Default metodların üstünlüklərindən bütün implementasiyalarda faydalanın.

```csharp
public interface INotifier
{
    void Notify(string message);

    void NotifyAll(string[] messages)
    {
        foreach (var message in messages)
        {
            Notify(message);
        }
    }
}
```

---

## 5. Test Edilə Bilənliyi Nəzərə Almamaq

❌ **Yanlış İstifadə:** Default metodları test baxımından çətinləşdirmək.

```csharp
public interface IService
{
    void PerformAction();

    void DefaultAction()
    {
        Console.WriteLine("Default action performed.");
    }
}
```

✅ **Düzgün İstifadə:** Default metodları bir baza sinfi və ya "dependency" vasitəsilə test edilə bilən hala gətirin.

```csharp
public interface IService
{
    void PerformAction();

    void DefaultAction()
    {
        PerformAction();
    }
}
```