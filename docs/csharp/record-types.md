# Record Types

C# dilində Record Types, immutable(dəyişməz) məlumat modelləri və dəyər əsaslı bərabərlik müqayisələri yaratmaq üçün istifadə olunan yeni bir tipdir. Yanlış istifadə halları Record Type'ların üstünlüklərini azalda bilər.

---

## 1. Record'ları Immutable Quruluşda Saxlamamaq

❌ **Yanlış İstifadə:** Record'ların property'lərini dəyişdirilə bilən (`mutable`) etmək.

```csharp
public record Person
{
	public string Name { get; set; } // Dəyişdirilə bilən
}
```

✅ **Düzgün İstifadə:** Record'ları immutable quruluşda saxlayaraq məlumat bütövlüyünü təmin edin.

```csharp
public record Person(string Name);
```

---

## 2. Bərabərlik Müqayisələrini Yanlış Qurmaq

❌ **Yanlış İstifadə:** Bərabərlik müqayisələri üçün `class` istifadə etmək.

```csharp
public class Person
{
	public string Name { get; set; }
}

// Reference bərabərliyi yoxlanılır
var p1 = new Person { Name = "Murat" };
var p2 = new Person { Name = "Murat" };
Console.WriteLine(p1 == p2); // False
```

✅ **Düzgün İstifadə:** Record Type istifadə edərək dəyərləri müqayisə etmək.

```csharp
public record Person(string Name);

var p1 = new Person("Murat");
var p2 = new Person("Murat");
Console.WriteLine(p1 == p2); // True
```

---

## 3. `with` Açar Sözünü Yanlış İstifadə Etmək

❌ **Yanlış İstifadə:** `with` ifadəsini istifadə etmədən məlumatı dəyişdirməyə çalışmaq.

```csharp
var person = new Person("Murat");
person.Name = "Derin"; // Derleme hatası
```

✅ **Düzgün İstifadə:** `with` açar sözünü istifadə edərək yeni bir Record nümunəsi yaradın.

```csharp
var person = new Person("Murat");
var updatedPerson = person with { Name = "Derin" };
```

---

## 4. Məlumat Modeli Üçün Yanlış Quruluş Seçimi

❌ **Yanlış İstifadə:** Record Type yerinə `class` və ya `struct` istifadə etmək.

```csharp
public class Address
{
	public string City { get; set; }
}
```

✅ **Düzgün İstifadə:** Immutable və dəyər əsaslı bərabərlik tələb edən hallar üçün Record Type istifadə edin.

```csharp
public record Address(string City);
```

---

## 5. Record'ların Performans Xüsusiyyətlərini Gözardı Etmək

❌ **Yanlış İstifadə:** Böyük məlumat quruluşları üçün Record Type istifadə etmək.

```csharp
public record LargeRecord(string Data); // Performans problemlərinə səbəb ola bilər
```

✅ **Düzgün İstifadə:** Böyük məlumat quruluşları üçün `class` istifadə etməyi düşünün.

```csharp
public class LargeRecord
{
	public string Data { get; set; }
}
```

---

## 6. Record'ları Yanlış Sahədə İstifadə Etmək

❌ **Yanlış İstifadə:** Record Type'ları DTO (Data Transfer Object) xaricində istifadə etmək.

```csharp
public record Repository(string Name); // Yanlış istifadə, record yerinə class istifadə edilməli
```

✅ **Düzgün İstifadə:** Record Type'ları DTO və məlumat modelləri üçün istifadə edin.

```csharp
public record PersonDto(string Name, int Age);
```