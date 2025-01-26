# Immutable Collections

Immutable collections, məlumat strukturlarında dəyişiklik edilməsini əngəlləyərək məlumatın tutarlılığını təmin edir. Performans üstünlükləri və eyni zamanda işləmələrdə etibarlılıq təqdim edir. Ancaq, yanlış istifadəsi lazımsız yaddaş istifadəsinə və mürəkkəbliyə səbəb ola bilər.

---

## 1. Immutable Collections Yerinə Mutable Collections İstifadə Etmək

❌ **Yanlış İstifadə:** Məlumat tutarlılığı tələb edən hallarda mutable kolleksiyaları istifadə etmək.

```csharp
var list = new List<int> { 1, 2, 3 };
list.Add(4); // Siyahı dəyişdirilə bilər
Console.WriteLine(string.Join(", ", list));
```

✅ **Düzgün İstifadə:** Dəyişdirilə bilməyən bir kolleksiya istifadə edərək məlumat tutarlılığını qoruyun.

```csharp
var immutableList = ImmutableList.Create(1, 2, 3);
var newList = immutableList.Add(4); // Yeni bir kolleksiya yaradılır
Console.WriteLine(string.Join(", ", newList));
```

---

## 2. Performansı Gözardı Etmək

❌ **Yanlış İstifadə:** Böyük məlumat dəstlərində lazımsız immutable dönüşümlər etmək.

```csharp
var list = new List<int>();
for (int i = 0; i < 1000; i++)
{
	list = list.Append(i).ToList(); // Hər dönüşümdə yeni bir siyahı yaradılır
}
```

✅ **Düzgün İstifadə:** Immutable kolleksiyalarla performans dostu əməliyyatlar həyata keçirin.

```csharp
var builder = ImmutableList.CreateBuilder<int>();
for (int i = 0; i < 1000; i++)
{
	builder.Add(i);
}
var immutableList = builder.ToImmutable();
Console.WriteLine(string.Join(", ", immutableList));
```

---

## 3. Immutable Collections'ı Yanlış Anlamaq

❌ **Yanlış İstifadə:** Immutable kolleksiyanın mövcud kolleksiyanı dəyişdirdiyini düşünmək.

```csharp
var immutableList = ImmutableList.Create(1, 2, 3);
immutableList.Add(4); // Yeni kolleksiya yaradır, amma təyin edilmir
Console.WriteLine(string.Join(", ", immutableList)); // Köhnə siyahı
```

✅ **Düzgün İstifadə:** Dəyişiklikləri yeni bir kolleksiyaya təyin edin.

```csharp
var immutableList = ImmutableList.Create(1, 2, 3);
var updatedList = immutableList.Add(4);
Console.WriteLine(string.Join(", ", updatedList)); // Yenilənmiş siyahı
```

---

## 4. Lazımsız Məlumat Kopyalama

❌ **Yanlış İstifadə:** Hər əməliyyatda yeni bir immutable kolleksiya yaratmaq.

```csharp
var immutableList = ImmutableList.Create<int>();
for (int i = 0; i < 100; i++)
{
	immutableList = immutableList.Add(i); // Hər dəfə yeni bir siyahı yaradılır
}
```

✅ **Düzgün İstifadə:** Builder obyekti ilə lazımsız kopyalamalardan qaçının.

```csharp
var builder = ImmutableList.CreateBuilder<int>();
for (int i = 0; i < 100; i++)
{
	builder.Add(i);
}
var immutableList = builder.ToImmutable();
Console.WriteLine(string.Join(", ", immutableList));
```

---

## 5. Yanlış Kolleksiya Növünü İstifadə Etmək

❌ **Yanlış İstifadə:** Ehtiyaca uyğun olmayan immutable kolleksiya növlərini istifadə etmək.

```csharp
var immutableStack = ImmutableStack<int>.Empty;
immutableStack = immutableStack.Push(1);
immutableStack = immutableStack.Push(2);
Console.WriteLine(immutableStack.Peek()); // 2
```

✅ **Düzgün İstifadə:** Əməliyyat növünə uyğun immutable kolleksiya seçin.

```csharp
var immutableQueue = ImmutableQueue<int>.Empty;
immutableQueue = immutableQueue.Enqueue(1);
immutableQueue = immutableQueue.Enqueue(2);
Console.WriteLine(immutableQueue.Peek()); // 1
```