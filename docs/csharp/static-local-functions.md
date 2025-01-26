# Static Lokal Funksiyalar

Static lokal funksiyalar, C# dilində performans və təhlükəsizlik üstünlükləri təmin etmək üçün istifadə edilə bilər. Bu metodlar, xarici əhatədəki heç bir dəyişənə çata bilməz və buna görə də yaddaş istifadəsini optimallaşdırır. Lakin, yanlış istifadələri kodun performansını aşağı sala və anlaşıqlılığını çətinləşdirə bilər.

---

## 1. Lazımsız Static Lokal Funksiya İstifadəsi

❌ **Yanlış İstifadə:** Static lokal funksiyaları lazımsız hallarda istifadə etmək.

```csharp
void CalculateSum(int a, int b)
{
    static int Add(int x, int y) => x + y; // Lazımsız static istifadəsi
    Console.WriteLine(Add(a, b));
}
```

✅ **Düzgün İstifadə:** Static lokal funksiyaları yalnız xarici əhatəyə giriş lazım olmadıqda istifadə edin.

```csharp
void CalculateSum(int a, int b)
{
    int Add(int x, int y) => x + y;
    Console.WriteLine(Add(a, b));
}
```

---

## 2. Xarici Dəyişənlərə Çatmağa Çalışmaq

❌ **Yanlış İstifadə:** Static lokal funksiya içində xarici çatmağa çalışmaq

```csharp
int multiplier = 2;
void MultiplyAndPrint(int number)
{
    static int Multiply(int x) => x * multiplier; // Səhv: Static metod xarici dəyişənlərə çata bilməz
    Console.WriteLine(Multiply(number));
}
```

✅ **Düzgün İstifadə:** Lazım olan məlumatı parametr olaraq ötürün.

```csharp
int multiplier = 2;
void MultiplyAndPrint(int number)
{
    static int Multiply(int x, int factor) => x * factor;
    Console.WriteLine(Multiply(number, multiplier));
}
```

---

## 3. Mənasız Adlandırma

❌ **Yanlış İstifadə:** Static lokal funksiya üçün mənasız və qısa adlar istifadə etmək.

```csharp
void ProcessData(int data)
{
    static int Fn(int x) => x * 2;
    Console.WriteLine(Fn(data));
}
```

✅ **Düzgün İstifadə:** Metod adlarını açıqlayıcı və mənalı seçin.

```csharp
void ProcessData(int data)
{
    static int DoubleValue(int x) => x * 2;
    Console.WriteLine(DoubleValue(data));
}
```

---

## 4. Performans Yaxşılaşdırmalarını Nəzərə Almamaq

❌ **Yanlış İstifadə:** Performans üstünlüyü təmin etməyəcək vəziyyətdə static lokal funksiya istifadə etmək.

```csharp
void PrintNumbers()
{
    static void Print(int x) => Console.WriteLine(x);
    for (int i = 0; i < 5; i++)
    {
        Print(i); // Performans fərqi yaratmaz
    }
}
```

✅ **Düzgün İstifadə:** Performans kritik vəziyyətlərdə static lokal funksiya seçin.

```csharp
void ProcessLargeData(int[] numbers)
{
    static int Process(int x) => x * 2;
    foreach (var number in numbers)
    {
        Console.WriteLine(Process(number));
    }
}
```

---

## 5. Lazımsız Asılılıqlar Əlavə Etmək

❌ **Yanlış İstifadə:** Static lokal funksiyada xarici metodlara lazımsız asılılıq əlavə etmək.

```csharp
void ProcessNumbers()
{
    static int AddAndDouble(int x, int y)
    {
        return (x + y) * 2;
    }
    Console.WriteLine(AddAndDouble(3, 5));
}
```

✅ **Düzgün İstifadə:** Lazım olan asılılıqları minimuma endirin.

```csharp
void ProcessNumbers()
{
    static int DoubleSum(int x, int y) => (x + y) * 2;
    Console.WriteLine(DoubleSum(3, 5));
}
```
