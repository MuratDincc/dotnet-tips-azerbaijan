# Unit Testinq və Mockinq

Unit testinq və mockinq, düzgün tətbiq edildikdə proqram layihələrinin etibarlılığını artırır. Lakin bu alətlərin yanlış istifadəsi həm test proseslərini mürəkkəbləşdirir, həm də kodun keyfiyyətinə mənfi təsir göstərir. Burada tez-tez edilən səhvlər və düzgün istifadə nümunələri göstərilib.

---

## 1. Tək Davranışı Test Etmək

❌ **Yanlış İstifadə:** Bir testin birdən çox davranışı yoxlamağa çalışması, testlərin məqsədini mürəkkəbləşdirir.

```csharp
[Fact]
public void AddAndSubtract_ShouldReturnCorrectResults()
{
    var calculator = new Calculator();
    var addResult = calculator.Add(2, 3);
    var subtractResult = calculator.Subtract(5, 3);

    Assert.Equal(5, addResult);
    Assert.Equal(2, subtractResult);
}
```

✅ **Düzgün İstifadə:** Hər davranışı ayrı bir testdə yoxlayın.

```csharp
[Fact]
public void Add_ShouldReturnSum()
{
    var calculator = new Calculator();
    var result = calculator.Add(2, 3);
    Assert.Equal(5, result);
}

[Fact]
public void Subtract_ShouldReturnDifference()
{
    var calculator = new Calculator();
    var result = calculator.Subtract(5, 3);
    Assert.Equal(2, result);
}
```

---

## 2. Mocklama ilə Həddindən Artıq Mürəkkəblik

❌ **Yanlış İstifadə:** Mocklama, yalnız gərəksiz bir abstraksiya qatı əlavə edirsə faydasızdır.

```csharp
var mockCalculator = new Mock<ICalculator>();
mockCalculator.Setup(calc => calc.Add(2, 3)).Returns(5);

var result = mockCalculator.Object.Add(2, 3);

Assert.Equal(5, result); // Mock burada gərəksizdir.
```

✅ **Düzgün İstifadə:** Mocklama, yalnız asılılıqları təcrid etmək üçün istifadə olunmalıdır.

```csharp
var mockWeatherService = new Mock<IWeatherService>();
mockWeatherService.Setup(service => service.GetWeather()).Returns("Günəşli");

var reporter = new WeatherReporter(mockWeatherService.Object);
var result = reporter.Report();

Assert.Equal("Günəşli", result);
```

---

## 3. Testlər Arasında Asılılıq Yaratmaq

❌ **Yanlış İstifadə:** Bir test, başqa bir testin nəticələrindən asılı olmamalıdır.

```csharp
[Fact]
public void Test_AddAndSubtract()
{
    var calculator = new Calculator();
    var sum = calculator.Add(2, 3);
    var difference = calculator.Subtract(sum, 2); // Bu test, Add metoduna asılıdır.

    Assert.Equal(3, difference);
}
```

✅ **Düzgün İstifadə:** Testlər müstəqil olmalı və bir-birindən təsirlənməməlidir.

```csharp
[Fact]
public void Add_ShouldReturnSum()
{
    var calculator = new Calculator();
    var result = calculator.Add(2, 3);
    Assert.Equal(5, result);
}

[Fact]
public void Subtract_ShouldReturnDifference()
{
    var calculator = new Calculator();
    var result = calculator.Subtract(5, 2);
    Assert.Equal(3, result);
}
```

---

## 4. Assert İstifadəsini Nəzərə Almamaq

❌ **Yanlış İstifadə:** Test nəticələrini sadəcə konsola yazdırmaq yetərsizdir.

```csharp
[Fact]
public void Add_ShouldPrintResult()
{
    var calculator = new Calculator();
    var result = calculator.Add(2, 3);
    Console.WriteLine(result); // Konsol çıxışı kifayət deyil.
}
```

✅ **Düzgün İstifadə:** `Assert` ifadələri ilə nəticələri yoxlayın.

```csharp
[Fact]
public void Add_ShouldReturnSum()
{
    var calculator = new Calculator();
    var result = calculator.Add(2, 3);
    Assert.Equal(5, result);
}
```

---

## 5. Parametrli Testlər İstifadə Etməmək

🔴 **Yanlış İstifadə:** Hər verilən dəsti üçün ayrı test yazmaq gərəksiz təkrara səbəb olur.

```csharp
[Fact]
public void Add_ShouldReturn5()
{
    var calculator = new Calculator();
    var result = calculator.Add(2, 3);
    Assert.Equal(5, result);
}

[Fact]
public void Add_ShouldReturnNegative2()
{
    var calculator = new Calculator();
    var result = calculator.Add(-1, -1);
    Assert.Equal(-2, result);
}
```

✅ **Düzgün İstifadə:** Parametrli testlərlə təkrarı azaldın.

```csharp
[Theory]
[InlineData(2, 3, 5)]
[InlineData(-1, -1, -2)]
[InlineData(0, 0, 0)]
public void Add_ShouldReturnSum(int a, int b, int expected)
{
    var calculator = new Calculator();
    var result = calculator.Add(a, b);
    Assert.Equal(expected, result);
}
```

---

## 6. Mock Obyektləri Yanlış Konumlandırmaq

🔴 **Yanlış İstifadə:** Mock obyektlərini metod səviyyəsində yaratmaq kod mürəkkəbliyini artırır.

```csharp
[Fact]
public void Report_ShouldReturnWeather()
{
    var mockWeatherService = new Mock<IWeatherService>(); // Mock burada yaradılıb
    mockWeatherService.Setup(service => service.GetWeather()).Returns("Günəşli");

    var reporter = new WeatherReporter(mockWeatherService.Object);
    var result = reporter.Report();

    Assert.Equal("Günəşli", result);
}
```

✅ **Düzgün İstifadə:** Mock obyektlərini sinif səviyyəsində qurun.

```csharp
public class WeatherReporterTests
{
    private readonly Mock<IWeatherService> _mockWeatherService;
    private readonly WeatherReporter _reporter;

    public WeatherReporterTests()
    {
        _mockWeatherService = new Mock<IWeatherService>();
        _reporter = new WeatherReporter(_mockWeatherService.Object);
    }

    [Fact]
    public void Report_ShouldReturnWeather()
    {
        _mockWeatherService.Setup(service => service.GetWeather()).Returns("Günəşli");
        var result = _reporter.Report();
        Assert.Equal("Günəşli", result);
    }
}
```
