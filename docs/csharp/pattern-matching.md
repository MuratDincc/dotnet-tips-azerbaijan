# Pattern Matching

C# dilində Pattern Matching, daha oxunaqlı və davamlı kod yazmağı təmin edən güclü bir vasitədir. Ancaq, yanlış istifadələr performans problemlərinə və mürəkkəb kod strukturlarına səbəb ola bilər.

---

## 1. `is` Operatoru ilə Lazımsız Tip Dəyişmələri

❌ **Yanlış İstifadə:** Tip yoxlamasından sonra manual tip dəyişməsi.

```csharp
if (obj is string)
{
	var str = (string)obj;
	Console.WriteLine(str.ToUpper());
}
```

✅ **Düzgün İstifadə:** `is` operatorunda birbaşa tip dəyişməsini istifadə edin.

```csharp
if (obj is string str)
{
	Console.WriteLine(str.ToUpper());
}
```

---

## 2. `switch` İfadələrində Sabit Dəyərlərdən İstifadə Etməmək

❌ **Yanlış İstifadə:** `switch` ifadələrində sabit dəyərlər yerinə mürəkkəb ifadələr istifadə etmək.

```csharp
switch (input.Length)
{
	case int n when n > 5:
		Console.WriteLine("Uzun bir mətn.");
		break;
	default:
		Console.WriteLine("Qısa bir mətn.");
		break;
}
```

✅ **Düzgün İstifadə:** Sabit dəyərlər istifadə edərək kodun oxunaqlığını artırın.

```csharp
switch (input.Length)
{
	case > 5:
		Console.WriteLine("Uzun bir mətn.");
		break;
	default:
		Console.WriteLine("Qısa bir mətn.");
		break;
}
```

---

## 3. `when` Şərtlərini Yanlış İstifadə Etmək

❌ **Yanlış İstifadə:** `when` şərtlərində lazımsız yoxlama etmək.

```csharp
if (obj is int i && i > 10)
{
	Console.WriteLine("10-dan böyük bir rəqəm.");
}
```

✅ **Düzgün İstifadə:** `when` ifadəsini `is` ilə inteqrasiya edərək kodu sadələşdirin.

```csharp
if (obj is int i when i > 10)
{
	Console.WriteLine("10-dan böyük bir rəqəm.");
}
```

---

## 4. `switch` İfadələrində Tip Yoxlamalarını Mürəkkəbləşdirmək

❌ **Yanlış İstifadə:** Fərqli tiplər üçün ayrı `if` blokları istifadə etmək.

```csharp
if (obj is string str)
{
	Console.WriteLine($"Mətn: {str}");
}
else if (obj is int num)
{
	Console.WriteLine($"Rəqəm: {num}");
}
```

✅ **Düzgün İstifadə:** `switch` ifadəsini istifadə edərək tip yoxlamalarını tənzimləyin.

```csharp
switch (obj)
{
	case string str:
		Console.WriteLine($"Mətn: {str}");
		break;
	case int num:
		Console.WriteLine($"Rəqəm: {num}");
		break;
	default:
		Console.WriteLine("Naməlum tip.");
		break;
}
```

---

## 5. Destructuring ilə Pattern Matching'i Nəzərə Almamag

❌ **Yanlış İstifadə:** Mürəkkəb məlumat strukturlarını manual həll etmək.

```csharp
if (point is Point)
{
	var p = (Point)point;
	Console.WriteLine($"X: {p.X}, Y: {p.Y}");
}
```

✅ **Düzgün İstifadə:** Destructuring ilə kodu sadələşdirin.

```csharp
if (point is Point(var x, var y))
{
	Console.WriteLine($"X: {x}, Y: {y}");
}
```

---

## 6. Performansı Nəzərə Almamaq

❌ **Yanlış İstifadə:** Böyük məlumat strukturlarında Pattern Matching'i optimizasiya etmədən istifadə etmək.

```csharp
foreach (var item in collection)
{
	if (item is string str && str.Contains("test"))
	{
		Console.WriteLine(str);
	}
}
```

✅ **Düzgün İstifadə:** Pattern Matching'i erkən çıxış mexanizmləri ilə birləşdirin.

```csharp
foreach (var str in collection.OfType<string>())
{
	if (str.Contains("test"))
	{
		Console.WriteLine(str);
	}
}
```