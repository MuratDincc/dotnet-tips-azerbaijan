# Null Handling

C# dilində `null` dəyərlərin yanlış idarə edilməsi, runtime xətalarına və gözlənilməz davranışlara səbəb ola bilər.

---

## 1. Null Nəzarətlərini Nəzərə Almamaq

❌ **Yanlış İstifadə:** `null` hallarını nəzarət etməmək.

```csharp
public void PrintMessage(string message)
{
	Console.WriteLine(message.Length); // NullReferenceException baş verə bilər
}
```

✅ **Düzgün İstifadə:** `null` nəzarətlərini edərək xətaların qarşısını alın.

```csharp
public void PrintMessage(string message)
{
	if (message == null) throw new ArgumentNullException(nameof(message));
	Console.WriteLine(message.Length);
}
```

---

## 2. `null` Yerinə Magic Value İstifadəsi

❌ **Yanlış İstifadə:** `null` yerinə mənasız bir magic value istifadə etmək.

```csharp
public string GetMessage() => "";
```

✅ **Düzgün İstifadə:** `null` üçün doğru bir model istifadə edərək daha oxunaqlı kod yazın.

```csharp
public string GetMessage() => null;
```

---

## 3. Null Coalescing Operatorunu İstifadə Etməmək

❌ **Yanlış İstifadə:** `null` nəzarətini manuel olaraq etmək.

```csharp
var result = value != null ? value : "Default";
```

✅ **Düzgün İstifadə:** Null coalescing operatoru (`??`) istifadə edərək kodu sadələşdirin.

```csharp
var result = value ?? "Default";
```

---

## 4. Null Conditional Operatorunu Nəzərə Almamaq

❌ **Yanlış İstifadə:** Null nəzarəti etmədən zəncirvari property'ə çatmaq.

```csharp
var length = person.Address.City.Length; // NullReferenceException riski
```

✅ **Düzgün İstifadə:** Null conditional operatorunu (`?.`) istifadə edərək xətaların qarşısını alın.

```csharp
var length = person?.Address?.City?.Length;
```

---

## 5. `Nullable<T>` İstifadəsini Gözardı Etmək

❌ **Yanlış İstifadə:** Nullable dəyər tipləri ilə işləyərkən manuel null nəzarəti etmək.

```csharp
int? number = null;
if (number.HasValue) Console.WriteLine(number.Value);
```

✅ **Düzgün İstifadə:** `Nullable<T>` xüsusiyyətlərini istifadə edərək kodu sadələşdirin.

```csharp
int? number = null;
Console.WriteLine(number ?? 0); // Varsayılan dəyəri istifadə edir
```

---

## 6. `null` Döndərən Metodlar İstifadə Etmək

❌ **Yanlış İstifadə:** `null` döndərən metodlar istifadə edərək xətaya açıq bir struktur yaratmaq.

```csharp
public string GetData()
{
	return null; // NullReferenceException riski
}
```

✅ **Düzgün İstifadə:** Null Object Pattern və ya alternativ bir həll istifadə edin.

```csharp
public string GetData()
{
	return string.Empty; // Null yerinə boş bir dəyər döndərir
}
```

---

## 7. `ArgumentNullException` ilə Detal Təmin Etməmək

❌ **Yanlış İstifadə:** `ArgumentNullException` istifadə edərkən detal təmin etməmək.

```csharp
throw new ArgumentNullException();
```

✅ **Düzgün İstifadə:** Parametr adı və açıqlama əlavə edərək detal təmin edin.

```csharp
throw new ArgumentNullException(nameof(parameter), "Parametr boş ola bilməz.");
```