# Target-Typed New

C# 9.0 ilə gələn target-typed `new` xüsusiyyəti, tip çıxarılmasını asanlaşdıraraq kodunuzu daha qısa və oxunaqlı hala gətirir. Lakin, yanlış istifadələri kodun anlaşıqlılığını və baxımını çətinləşdirə bilər.

---

## 1. Hədəf Tipin Qeyri-Müəyyən Olduğu Vəziyyətlər

❌ **Yanlış İstifadə:** Hədəf tipin açıq olmadığı vəziyyətlərdə target-typed `new` istifadə etmək.

```csharp
var person = new(); // Hansı tip olduğu anlaşıla bilməz
person.Name = "Murat";
```

✅ **Düzgün İstifadə:** Hədəf tipin aydın bir şəkildə göstərildiyi vəziyyətlərdə istifadə edin.

```csharp
Person person = new();
person.Name = "Murat";
```

**Tərif:**
```csharp
public class Person
{
    public string Name { get; set; }
}
```

---

## 2. Mürəkkəb İfadələrdə Target-Typed `new` İstifadə Etmək

❌ **Yanlış İstifadə:** Target-typed `new`'i mürəkkəb ifadələrdə istifadə edərək kodu daha az oxunaqlı hala gətirmək.

```csharp
var person = new("Murat", 33); // Xüsusilə birdən çox konstruktor varsa qeyri-müəyyənlik yarada bilər
```

✅ **Düzgün İstifadə:** Target-typed `new`'i sadə ifadələrdə istifadə edin.

```csharp
Person person = new("Murat", 33);
```

**Konstruktor Tərifi:**
```csharp
public Person(string name, int age)
{
    Name = name;
    Age = age;
}
```

---

## 3. Kolleksiyalarda İstifadəsini Yanlış İdarə Etmək

❌ **Yanlış İstifadə:** Kolleksiya yaradarkən hədəf tipi göstərməmək.

```csharp
var people = new List<Person>
{
    new() { Name = "Murat" },
    new() { Name = "Derin" }
};
```

✅ **Düzgün İstifadə:** Kolleksiyanın tipini açıq şəkildə göstərin.

```csharp
List<Person> people = new()
{
    new() { Name = "Murat" },
    new() { Name = "Derin" }
};
```

---

## 4. Adlandırılmış Arqumentlərlə Səhv İstifadə

❌ **Yanlış İstifadə:** Adlandırılmış arqumentlərlə target-typed `new` istifadəsi qeyri-müəyyənlik yarada bilər.

```csharp
var person = new(name: "Murat", age: 33);
```

✅ **Düzgün İstifadə:** Adlandırılmış arqumentlər istifadə edərkən hədəf tipi aydınlaşdırın.

```csharp
Person person = new(name: "Murat", age: 33);
```

---

## 5. Target-Typed `new` və `Nullable` Tiplər

❌ **Yanlış İstifadə:** Nullable tiplərlə target-typed `new` istifadəsi yanlış anlaşılmalara yol aça bilər.

```csharp
Person? person = new(); // Nullable amma hansı konstruktorun çağırıldığı qeyri-müəyyəndir
```

✅ **Düzgün İstifadə:** Nullable tiplərlə istifadədə hədəf tipi aydınlaşdırın.

```csharp
Person? person = new Person();
```

---

## 6. Test Edilə Bilirliyini Nəzərə Almamaq

❌ **Yanlış İstifadə:** Test edilə bilirlik baxımından target-typed `new`'in təsirini nəzərə almamaq.

```csharp
var service = new();
```

✅ **Düzgün İstifadə:** Tipi aydın bir şəkildə göstərin və test edilə bilirliyi artırın.

```csharp
IService service = new ServiceImplementation();
```

**Tərif:**
```csharp
public interface IService { }
public class ServiceImplementation : IService { }
```
