# Safe Casting with `as`

C# dilində `as` açar sözü, tip çevirmələrini təhlükəsiz şəkildə həyata keçirmək üçün istifadə olunan bir vasitədir. Yanlış istifadələr, gözlənilməz səhvlərə və kod mürəkkəbliyinə səbəb ola bilər.

---

## 1. `as` İstifadəsini Yanlış İdarə Etmək

❌ **Yanlış İstifadə:** `as` çevirməsindən sonra null yoxlaması etməmək.

```csharp
object obj = "Hello, World!";
string message = obj as string;
Console.WriteLine(message.Length); // NullReferenceException riski
```

✅ **Düzgün İstifadə:** `as` çevirməsindən sonra null yoxlaması edərək exception'ın qarşısını alın.

```csharp
object obj = "Hello, World!";
string message = obj as string;
if (message != null)
{
	Console.WriteLine(message.Length);
}
else
{
	Console.WriteLine("Çevirme uğursuz oldu.");
}
```

---

## 2. `as` Yerinə Yanlış Cast İstifadəsi

❌ **Yanlış İstifadə:** Tip çevirməsində birbaşa cast istifadə etmək.

```csharp
object obj = 123;
string text = (string)obj; // InvalidCastException
```

✅ **Düzgün İstifadə:** Tip çevirməsində təhlükəsiz şəkildə `as` istifadə edin.

```csharp
object obj = 123;
string text = obj as string;
if (text == null)
{
	Console.WriteLine("Çevirmə uğursuz oldu.");
}
```

---

## 3. Hədəf Tipi Yanlış Müəyyən Etmək

❌ **Yanlış İstifadə:** Uyğunsuz hədəf tiplə `as` çevirməsi etmək.

```csharp
object obj = new List<int>();
var str = obj as string; // Null qaytaracaq çünki tip uyğun gəlmir
```

✅ **Düzgün İstifadə:** Hədəf tipi düzgün şəkildə müəyyən etmək.

```csharp
object obj = new List<int>();
var list = obj as List<int>;
if (list != null)
{
	Console.WriteLine($"Listdə {list.Count} obyekt var.");
}
```

---

## 4. Alternativ Yoxlamaları Gözardı Etmək

❌ **Yanlış İstifadə:** Yalnız `as` istifadə edərək çevirmə yoxlaması etmək.

```csharp
object obj = "Test String";
string text = obj as string;
if (text != null)
{
	Console.WriteLine(text.ToUpper());
}
```

✅ **Düzgün İstifadə:** `is` ifadəsi ilə çevirmənin uyğunluğunu yoxlayın.

```csharp
object obj = "Test String";
if (obj is string text)
{
	Console.WriteLine(text.ToUpper());
}
```

---

## 5. Mürəkkəb Yoxlamaları `as` ilə Birləşdirmək

❌ **Yanlış İstifadə:** Çoxlu yoxlamaları birləşdirərək kodu mürəkkəb hala gətirmək.

```csharp
object obj = "Hello";
if (obj != null && obj is string && obj.ToString().Length > 5)
{
	Console.WriteLine("Keçərli string.");
}
```

✅ **Düzgün İstifadə:** Kodun oxunaqlığını artırmaq üçün yoxlamanı sadələşdirin.

```csharp
if (obj is string text && text.Length > 5)
{
	Console.WriteLine("Keçərli string.");
}
```