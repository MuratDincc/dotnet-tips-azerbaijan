# Ranges and Indices

C# 8.0 ilə təqdim olunan Ranges (`..`) və Indices (`^`) xüsusiyyətləri, kolleksiyalar üzərində daha oxunaqlı və qısa əməliyyatlar etməyinizi təmin edir. Lakin, bu xüsusiyyətlərin yanlış istifadəsi gözlənilməz nəticələrə və ya performans problemlərinə səbəb ola bilər.

---

## 1. Məntiqli İstifadəni Nəzərə Almamaq

❌ **Yanlış İstifadə:** Ənənəvi üsullarla lazımsız mürəkkəb əməliyyatlar etmək.

```csharp
var array = new int[] { 1, 2, 3, 4, 5 };
var lastThree = array.Skip(array.Length - 3).ToArray();
```

✅ **İdeal İstifadə:** Indices xüsusiyyətini istifadə edərək əməliyyatları sadələşdirin.

```csharp
var array = new int[] { 1, 2, 3, 4, 5 };
var lastThree = array[^3..];
```

---

## 2. Mənfi Indices İstifadəsini Yanlış Anlamaq

❌ **Yanlış İstifadə:** Mənfi indekslərin yanlış şərh edilməsi.

```csharp
var array = new int[] { 1, 2, 3, 4, 5 };
var invalidIndex = array[-1]; // Xəta
```

✅ **İdeal İstifadə:** Indices ilə son elementlərə doğru giriş təmin edin.

```csharp
var array = new int[] { 1, 2, 3, 4, 5 };
var lastElement = array[^1]; // Son element
```

---

## 3. Ranges ilə Performansı Nəzərə Almamaq

❌ **Yanlış İstifadə:** Böyük məlumat listlərində lazımsız kopyalamalar etmək.

```csharp
var data = Enumerable.Range(1, 1000000).ToArray();
var subset = data.Skip(100).Take(50).ToArray(); // Lazımsız kopyalama
```

✅ **İdeal İstifadə:** Ranges ilə performansı optimallaşdırın.

```csharp
var data = Enumerable.Range(1, 1000000).ToArray();
var subset = data[100..150]; // Kopyalama minimal
```

---

## 4. Kolleksiyalardan Kənarda Ranges İstifadəsi

❌ **Yanlış İstifadə:** Ranges və Indices xüsusiyyətlərini uyğun olmayan məlumat tiplərində istifadə etmək.

```csharp
string text = "Hello World";
var invalidRange = text[^5..]; // Yalnız siyahı tiplərində keçərlidir
```

✅ **İdeal İstifadə:** Ranges və Indices xüsusiyyətlərini doğru məlumat tipləri ilə istifadə edin.

```csharp
string text = "Hello World";
var substring = text[^5..]; // Keçərli və effektiv istifadə
```

---

## 5. Start və End Indices'i Yanlış Təyin Etmək

❌ **Yanlış İstifadə:** Başlanğıc və bitiş indekslərini qarışdırmaq.

```csharp
var array = new int[] { 1, 2, 3, 4, 5 };
var invalidRange = array[5..3]; // Xəta: Bitiş indeksi başlanğıcdan əvvəldir
```

✅ **İdeal İstifadə:** Ranges üçün doğru başlanğıc və bitiş indekslərini təyin edin.

```csharp
var array = new int[] { 1, 2, 3, 4, 5 };
var validRange = array[3..5]; // Doğru istifadə
```

---

## 6. Ranges və Indices'i Birlikdə İstifadə Etməyi Nəzərə Almamaq

❌ **Yanlış İstifadə:** Xüsusiyyətləri birlikdə istifadə etməmək.

```csharp
var array = new int[] { 1, 2, 3, 4, 5 };
var firstThree = array.Take(3).ToArray(); // Lazımsız mürəkkəblik
```

✅ **İdeal İstifadə:** Indices və Ranges'i birlikdə istifadə edərək daha təmiz bir struktur əldə edin.

```csharp
var array = new int[] { 1, 2, 3, 4, 5 };
var firstThree = array[..3]; // İlk üç element
```