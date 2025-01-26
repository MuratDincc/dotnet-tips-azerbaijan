# Tuples

C# dilində tuples, birdən çox dəyəri bir arada daşımaq üçün faydalı bir məlumat strukturudur. Lakin, yanlış istifadə vəziyyətləri kodun oxunaqlılığını və davamlılığını azalda bilər.

---

## 1. Mənasız Tuple Adları İstifadə Etmək

❌ **Yanlış İstifadə:** Tuple komponentlərini standart adlarla saxlamaq.

```csharp
var result = GetPerson();
Console.WriteLine(result.Item1); // Mənasız
Console.WriteLine(result.Item2);
```

✅ **Düzgün İstifadə:** Tuple komponentlərinə mənalı adlar verin.

```csharp
var (name, age) = GetPerson();
Console.WriteLine(name);
Console.WriteLine(age);
```

**Tərif:**
```csharp
(string Name, int Age) GetPerson() => ("Murat", 33);
```

---

## 2. Tuples Yerinə Sinifləri İstifadə Etməyi Nəzərə Almamaq

❌ **Yanlış İstifadə:** Mürəkkəb məlumat strukturları üçün tuple istifadə etmək.

```csharp
(string, int, string) GetDetailedPerson() => ("Murat", 33, "Istanbul");
```

✅ **Düzgün İstifadə:** Daha mürəkkəb məlumat strukturları üçün sinif və ya record strukturu istifadə edin.

```csharp
public record Person(string Name, int Age, string City);

Person GetDetailedPerson() => new("Murat", 33, "Istanbul");
```

---

## 3. Uzun Tuple Strukturları İstifadə Etmək

❌ **Yanlış İstifadə:** Çox sayda komponent ehtiva edən tuple tərifləri.

```csharp
(string, int, string, string, bool) GetComplexData() => ("Murat", 33, "Istanbul", "Yazılım Mimarı", true);
```

✅ **Düzgün İstifadə:** Daha qısa və mənalı tuple strukturları istifadə edin.

```csharp
(string Name, int Age) GetBasicData() => ("Murat", 33);
```

---

## 4. Tuple'ları Döngülərdə Yanlış İstifadə Etmək

❌ **Yanlış İstifadə:** Tuple komponentlərinə birbaşa indeks ilə çatmaq.

```csharp
var data = new List<(string, int)>
{
    ("Murat", 33),
    ("Derin", 2)
};

foreach (var item in data)
{
    Console.WriteLine($"Name: {item.Item1}, Age: {item.Item2}");
}
```

✅ **Düzgün İstifadə:** Tuple komponentlərini mənalı adlarla çatın.

```csharp
var data = new List<(string Name, int Age)>
{
    ("Murat", 33),
    ("Derin", 2)
};

foreach (var (name, age) in data)
{
    Console.WriteLine($"Name: {name}, Age: {age}");
}
```

---

## 5. Tuple'ları Geri Dönüş Dəyəri Olaraq Yanlış İstifadə Etmək

❌ **Yanlış İstifadə:** Açıq şəkildə təyin olunmamış tuple'ları metod dönüş dəyəri olaraq istifadə etmək.

```csharp
public (string, int) GetPerson() => ("Murat", 33);
```

✅ **Düzgün İstifadə:** Tuple dönüş dəyərlərini açıq şəkildə təyin edin.

```csharp
public (string Name, int Age) GetPerson() => ("Murat", 33);
```

---

## 6. Tuple Dəyərlərini Yanlış Dəyərləndirmək

❌ **Yanlış İstifadə:** Tuple'ları müqayisə edərkən bütün komponentləri yoxlamamaq.

```csharp
var tuple1 = ("Murat", 33);
var tuple2 = ("Murat", 2);

if (tuple1 == tuple2) // Xəta
{
    Console.WriteLine("Bərabər!");
}
```

✅ **Düzgün İstifadə:** Tuple müqayisələrində bütün komponentləri nəzərə alın.

```csharp
var tuple1 = ("Murat", 33);
var tuple2 = ("Murat", 33);

if (tuple1 == tuple2)
{
    Console.WriteLine("Bərabər!");
}
```
