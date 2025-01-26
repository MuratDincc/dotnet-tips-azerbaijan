# Ref Returns and Ref Locals

Ref returns və locals, yaddaşdakı məlumatlara birbaşa referans edərək performans təkmilləşdirmələri təklif edir. Lakin, yanlış istifadələr məlumat bütövlüyünü təhlükəyə ata bilər və gözlənilməz davranışlara səbəb ola bilər.

---

## 1. Lazımsız Ref İstifadəsi

❌ **Yanlış İstifadə:** Lazımsız hallarda `ref` istifadəsi.

```csharp
ref int GetFirstElement(ref int[] array)
{
	return ref array[0];
}
```

✅ **Düzgün İstifadə:** `ref` yalnız böyük məlumat strukturlarında və ya kritik performans tələb edən hallarda istifadə edilməlidir.

```csharp
ref int GetFirstElement(ref int[] array)
{
	if (array.Length == 0)
		throw new ArgumentException("Array boş ola bilməz.", nameof(array));
	return ref array[0];
}
```

---

## 2. Ref Returns ilə Məlumat Bütövlüyünü Təhlükəyə Atmaq

❌ **Yanlış İstifadə:** Geri qaytarılan istinadı təhlükəsiz olmayan şəkildə dəyişdirmək.

```csharp
ref int GetElement(ref int[] array, int index)
{
	return ref array[index];
}

// Məlumat yanlışlıqla dəyişdirilə bilər.
ref int element = ref GetElement(ref numbers, 2);
element = -1;
```

✅ **Düzgün İstifadə:** İstinadın istifadəsi təhlükəsiz hala gətirilməlidir.

```csharp
ref int GetElement(ref int[] array, int index)
{
	if (index < 0 || index >= array.Length)
		throw new IndexOutOfRangeException("Yanlış indeks.");
	return ref array[index];
}
```

---

## 3. Ref Locals İstifadəsini Lazımsız Hale Getirmək

❌ **Yanlış İstifadə:** Ref locals istifadəsini gərəksiz yerə mürəkkəbləşdirmək.

```csharp
int[] numbers = { 1, 2, 3, 4 };
ref int firstNumber = ref numbers[0];
firstNumber = 10; // Mürəkkəb istifadə
```

✅ **Düzgün İstifadə:** Ref locals yalnız yaddaş qənaəti və ya performans üçün lazımdırsa istifadə edilməlidir.

```csharp
ref int GetFirst(ref int[] array)
{
	return ref array[0];
}

ref int first = ref GetFirst(ref numbers);
first = 10;
```

---

## 4. Böyük Məlumat Strukturlarında Performansı Optimallaşdırmamaq

❌ **Yanlış İstifadə:** Böyük məlumat strukturlarında kopyalama əməliyyatı etmək.

```csharp
int[] largeArray = GetLargeArray();
int value = largeArray[0]; // Kopyalama
```

✅ **Düzgün İstifadə:** `ref` istifadə edərək lazımsız kopyalamaları önləmək.

```csharp
ref int GetLargeArrayFirstElement(ref int[] array)
{
	return ref array[0];
}

ref int value = ref GetLargeArrayFirstElement(ref largeArray);
```

---

## 5. `ref readonly` İstifadəsini Nəzərə Almamaq

❌ **Yanlış İstifadə:** Yalnız oxunacaq istinadlar üçün `ref` istifadə etmək.

```csharp
ref int GetReadOnlyValue(ref int[] array, int index)
{
	return ref array[index]; // Yazma riski mövcuddur
}
```

✅ **Düzgün İstifadə:** Yalnız oxuna bilən istinadlar üçün `ref readonly` istifadə edin.

```csharp
ref readonly int GetReadOnlyValue(ref int[] array, int index)
{
	if (index < 0 || index >= array.Length)
		throw new IndexOutOfRangeException("Yanlış indeks.");
	return ref array[index];
}
```

---

## 6. Ref İstifadəsini Doğru Dokumentasiya Etmək

❌ **Yanlış İstifadə:** `ref` parametrlərin mənasını açıqlamamaq.

```csharp
ref int Process(ref int number)
{
	number *= 2;
	return ref number;
}
```

✅ **Düzgün İstifadə:** `ref` parametrlərin məqsədini və təsirini açıqlamaq üçün şərh əlavə edin.

```csharp
/// <summary>
/// Verilən rəqəmi iki ilə vurur və istinadı geri qaytarır.
/// </summary>
/// <param name="number">İşlənəcək rəqəm.</param>
/// <returns>Yenilənmiş rəqəmin istinadı.</returns>
ref int Process(ref int number)
{
	number *= 2;
	return ref number;
}
```