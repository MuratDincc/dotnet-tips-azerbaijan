# Deconstruction

Deconstruction obyektin komponentlərini hissələrə ayıraraq daha oxunaqlı və nizamlı kod yazmağa imkan yaradır. Amma, yanlış istifadələr kodun mürəkkəbləşməsinə və səhvlərə səbəb ola bilər.

---

## 1. Deconstruction İstifadəsini Nəzərə Almamaq

❌ **Yanlış İstifadə:** Deconstruction yerinə manual təyin etmələr etmək.

```csharp
var point = GetPoint();
var x = point.X;
var y = point.Y;
Console.WriteLine($"X: {x}, Y: {y}");
```

✅ **Düzgün İstifadə:** Deconstruction istifadə edərək daha qısa və oxunaqlı kod yazın.

```csharp
var (x, y) = GetPoint();
Console.WriteLine($"X: {x}, Y: {y}");
```

**Metod Tanımı:**
```csharp
public (int X, int Y) GetPoint() => (10, 20);
```

---

## 2. Deconstruction Üçün Mənasız Adlar İstifadə Etmək

❌ **Yanlış İstifadə:** Deconstruction-da uyğunsuz adlardan istifadə etmək.

```csharp
var (a, b) = GetDimensions();
Console.WriteLine($"Width: {a}, Height: {b}");
```

✅ **Düzgün İstifadə:** Deconstruction zamanı mənalı dəyişən adlarından istifadə edin.

```csharp
var (width, height) = GetDimensions();
Console.WriteLine($"Width: {width}, Height: {height}");
```

**Metod Tanımı:**
```csharp
public (int Width, int Height) GetDimensions() => (1920, 1080);
```

---

## 3. Çox Mürəkkəb Strukturlar İstifadə Etmək

❌ **Yanlış İstifadə:** Mürəkkəb tiplərdə deconstruction etməyə çalışmaq.

```csharp
var data = GetComplexData();
var a = data.Item1;
var b = data.Item2.X;
var c = data.Item2.Y;
```

✅ **Düzgün İstifadə:** Deconstruction ilə daha nizamlı bir struktur istifadə edin.

```csharp
var (id, (x, y)) = GetComplexData();
Console.WriteLine($"ID: {id}, X: {x}, Y: {y}");
```

**Metod Tanımı:**
```csharp
public (int ID, (int X, int Y) Coordinates) GetComplexData() => (1, (10, 20));
```

---

## 4. Lazımsız Deconstruction Etmək

❌ **Yanlış İstifadə:** Sadə əməliyyatlar üçün lazımsız deconstruction.

```csharp
var (x, y) = (10, 20);
Console.WriteLine($"X: {x}, Y: {y}");
```

✅ **Düzgün İstifadə:** Lazımsız deconstruction-dan çəkinin.

```csharp
var point = (10, 20);
Console.WriteLine($"X: {point.Item1}, Y: {point.Item2}");
```

---

## 5. Nullable Tipləri Deconstruction ilə Yanlış İdarə Etmək

❌ **Yanlış İstifadə:** Nullable tiplərdə null yoxlaması etməmək.

```csharp
var (x, y) = GetNullablePoint(); // Səhv riski
```

✅ **Düzgün İstifadə:** Nullable tiplərdə təhlükəsiz deconstruction istifadə edin.

```csharp
var point = GetNullablePoint();
if (point.HasValue)
{
    var (x, y) = point.Value;
    Console.WriteLine($"X: {x}, Y: {y}");
}
else
{
    Console.WriteLine("Point is null.");
}
```

**Metod Tanımı:**
```csharp
public (int X, int Y)? GetNullablePoint() => null;
```