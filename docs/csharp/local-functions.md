# Local Functions

Local functions, bir metod daxilində təyin olunan və yalnız həmin metod daxilində istifadə olunan kiçik funksiyalardır. Bu, kodun oxunaqlılığını artırır, lazımsız metod təyinatlarının qarşısını alır və əhatə dairəsini daraldaraq kodun daha səliqəli olmasını təmin edir. Lakin, yanlış istifadələr performans problemlərinə və mürəkkəbliyə səbəb ola bilər.

---

## 1. Lazımsız Local Function İstifadəsi

❌ **Yanlış İstifadə:** Vacib bir tələbat olmadan local function təyin etmək.

```csharp
void PerformCalculation()
{
	void Helper()
	{
		Console.WriteLine("Hesablama tamamlandı.");
	}
	Helper();
}
```

✅ **Düzgün İstifadə:** Local functions yalnız kodun mənasını və oxunaqlılığını artırmaq üçün istifadə edilməlidir.

```csharp
void PerformCalculation()
{
	void AddNumbers(int x, int y) => Console.WriteLine($"Nəticə: {x + y}");
	AddNumbers(5, 10);
}
```

---

## 2. Parametr Keçidlərini Yanlış İstifadə Etmək

❌ **Yanlış İstifadə:** Lazımsız parametr keçidləri etmək.

```csharp
void PrintMessage(string message)
{
	void Display(string msg) => Console.WriteLine(msg);
	Display(message);
}
```

✅ **Düzgün İstifadə:** Lazımsız parametr keçidlərindən çəkinin.

```csharp
void PrintMessage(string message)
{
	void Display() => Console.WriteLine(message);
	Display();
}
```

---

## 3. Local Function'ı Yenidən İstifadə Edilə Bilən Hala Gətirməmək

❌ **Yanlış İstifadə:** Local function'ı yenidən istifadə edilə bilən kod parçaları üçün istifadə etmək.

```csharp
void ProcessData(int data)
{
	void MultiplyByTwo(int value) => Console.WriteLine(value * 2);
	MultiplyByTwo(data);
}
```

✅ **Düzgün İstifadə:** Yenidən istifadə edilə bilən kodlar üçün metodlar əvəzinə local function istifadəsini məhdudlaşdırmaq.

```csharp
void MultiplyByTwo(int value) => Console.WriteLine(value * 2);
MultiplyByTwo(5);
MultiplyByTwo(10);
```

---

## 4. Xətaları İdarə Etməmək

❌ **Yanlış İstifadə:** Local function'larda xətaları nəzərə almamaq.

```csharp
void PerformDivision(int x, int y)
{
	int Divide() => x / y; // Xəta riski
	Console.WriteLine(Divide());
}
```

✅ **Düzgün İstifadə:** Local function daxilində xəta idarəsini təmin edin.

```csharp
void PerformDivision(int x, int y)
{
	int Divide()
	{
		if (y == 0) throw new DivideByZeroException("Sıfıra bölmə xətası.");
		return x / y;
	}

	try
	{
		Console.WriteLine(Divide());
	}
	catch (DivideByZeroException ex)
	{
		Console.WriteLine($"Xəta: {ex.Message}");
	}
}
```

---

## 5. Adlandırma Standartlarını Nəzərə Etmək

❌ **Yanlış İstifadə:** Mənasız və qısa adlar istifadə etmək.

```csharp
void DoTask()
{
	void Fn() => Console.WriteLine("Tapşırıq tamamlandı.");
	Fn();
}
```

✅ **Düzgün İstifadə:** Local function'lar üçün mənalı adlar istifadə edin.

```csharp
void DoTask()
{
	void PrintCompletionMessage() => Console.WriteLine("Tapşırıq tamamlandı.");
	PrintCompletionMessage();
}
```

---

## 6. Lazımsız Asılılıq Əlavə Etmək

❌ **Yanlış İstifadə:** Local function'larda lazımsız asılılıqlar təyin etmək.

```csharp
void ProcessData(int value)
{
	string message = "Nəticə";
	void DisplayResult(int result) => Console.WriteLine($"{message}: {result}");
	DisplayResult(value * 2);
}
```

✅ **Düzgün İstifadə:** Asılılıqları minimuma endirin.

```csharp
void ProcessData(int value)
{
	void DisplayResult(int result) => Console.WriteLine($"Nəticə: {result}");
	DisplayResult(value * 2);
}
```