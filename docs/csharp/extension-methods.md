# Extension Methods

Extension methods, mövcud siniflərə və ya interfeyslərə yeni metodlar əlavə etməyin effektiv bir yoludur. Ancaq, yanlış istifadə halları kodun anlaşıqlıq və baxımını çətinləşdirə bilər.

---

## 1. Extension Methodları Yanlış Sahədə İstifadə Etmək

❌ **Yanlış İstifadə:** Extension method'ları lazımsız yerə ümumi (`global`) etmək.

```csharp
public static class GlobalExtensions
{
	public static string ToUpperCase(this string input) => input.ToUpper();
}
```

✅ **Düzgün İstifadə:** Extension method'ları müəyyən bir kontekst və ya məqsədə yönəlik sahə ilə məhdudlaşdırın.

```csharp
public static class StringExtensions
{
	public static string ToUpperCase(this string input) => input.ToUpper();
}
```

---

## 2. Extension Methodları Yanlış Adlandırmaq

❌ **Yanlış İstifadə:** Extension method'lara mənasız adlar vermək.

```csharp
public static string Func(this string input) => input.ToUpper();
```

✅ **Düzgün İstifadə:** Extension method'lara mənalı və izahlı adlar verin.

```csharp
public static string ToUpperCase(this string input) => input.ToUpper();
```

---

## 3. Extension Methodlarda Lazımsız Yoxlamalar

❌ **Yanlış İstifadə:** Lazım olmayan null yoxlamaları etmək.

```csharp
public static string SafeToUpperCase(this string input)
{
	if (input == null) return string.Empty;
	return input.ToUpper();
}
```

✅ **Düzgün İstifadə:** Lazımsız yoxlamalardan çəkinin və istifadəçini yönləndirin.

```csharp
public static string ToUpperCase(this string input) => input?.ToUpper() ?? throw new ArgumentNullException(nameof(input));
```

---

## 4. Lazımsız Parametrlər İstifadə Etmək

❌ **Yanlış İstifadə:** Extension method'da lazımsız əlavə parametrlər istifadə etmək.

```csharp
public static string AppendSuffix(this string input, string suffix)
{
	return input + suffix;
}
```

✅ **Düzgün İstifadə:** Lazımsız parametrlərdən çəkinin.

```csharp
public static string AppendSuffix(this string input, string suffix = "Default")
{
	return input + suffix;
}
```

---

## 5. Extension Methodları Mürəkkəbləşdirmək

❌ **Yanlış İstifadə:** Çox funksionallığı tək bir extension method'da birləşdirmək.

```csharp
public static string TransformText(this string input, bool toUpper, bool addSuffix)
{
	var result = input;
	if (toUpper) result = result.ToUpper();
	if (addSuffix) result += "_Suffix";
	return result;
}
```

✅ **Düzgün İstifadə:** Funksiyaları bir neçə extension method'a bölün.

```csharp
public static string ToUpperCase(this string input) => input.ToUpper();

public static string AddSuffix(this string input, string suffix) => input + suffix;
```

---

## 6. Extension Methodların İstifadəsini Dokunmentasiya Etmək

❌ **Yanlış İstifadə:** Extension method'ların necə istifadə ediləcəyini açıqlamamaq.

```csharp
public static string AddPrefix(this string input, string prefix)
{
	return prefix + input;
}
```

✅ **Düzgün İstifadə:** Extension method'ların istifadəsini şərhlərlə açıqlayın.

```csharp
/// <summary>
/// Verilən mətnin əvvəlinə bir prefiks əlavə edir.
/// </summary>
/// <param name="input">Orijinal mətn.</param>
/// <param name="prefix">Əlavə ediləcək prefiks.</param>
/// <returns>Prefiks əlavə edilmiş mətn.</returns>
public static string AddPrefix(this string input, string prefix)
{
	return prefix + input;
}
```