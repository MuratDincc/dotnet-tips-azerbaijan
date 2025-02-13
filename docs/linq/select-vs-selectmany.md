# LINQ: `Select` və `SelectMany` Fərqləri  

LINQ-də `Select` və `SelectMany`, məlumat projeksiyaları etmək üçün istifadə olunan güclü metodlardır. Lakin bu iki metod arasında vacib fərqlər mövcuddur. Düzgün metodu seçmək həm performans, həm də kodun oxunaqlılığı baxımından kritik əhəmiyyət daşıyır.  

---

## 1. `Select` Nədir?  

`Select`, hər bir elementi bir projeksiyadan keçirərək yeni bir kolleksiya yaradır. Bu metod, əsasən kolleksiyadakı elementləri çevirmək və ya müəyyən xüsusiyyətləri seçmək üçün istifadə olunur.  

✅ **Nümunə:**  

```csharp
var names = people.Select(p => p.Name).ToList();

foreach (var name in names)
{
    Console.WriteLine(name);
}
```

Bu nümunədə, `people` kolleksiyasındakı `Name` xüsusiyyətləri götürülərək yeni bir kolleksiya yaradılır.  

---

## 2. `SelectMany` Nədir?  

`SelectMany`, hər bir elementin daxilindəki kolleksiyaları düzləşdirərək tək bir kolleksiya halına gətirir. Bu metod, iç içə kolleksiyalarla işləyərkən olduqca faydalıdır.  

✅ **Nümunə:**  

```csharp
var allSubjects = students.SelectMany(s => s.Subjects).ToList();

foreach (var subject in allSubjects)
{
    Console.WriteLine(subject);
}
```

Bu nümunədə, hər bir tələbənin `Subjects` kolleksiyası düzləşdirilərək tək bir kolleksiya halına gətirilir.  

---

## 3. Yanlış və Düzgün İstifadə  

### **Yanlış İstifadə:** `Select` ilə düzləşdirməyə çalışmaq  

❌ **Yanlış İstifadə:**  

```csharp
var allSubjects = students.Select(s => s.Subjects).ToList();

foreach (var subjectList in allSubjects)
{
    foreach (var subject in subjectList)
    {
        Console.WriteLine(subject);
    }
}
```

Bu yanaşma, hər bir tələbənin `Subjects` siyahısını ayrıca emal edir və lazımsız mürəkkəblik yaradır.  

---

### **Düzgün İstifadə:** `SelectMany` ilə düzləşdirmə  

✅ **Düzgün İstifadə:**  

```csharp
var allSubjects = students.SelectMany(s => s.Subjects).ToList();

foreach (var subject in allSubjects)
{
    Console.WriteLine(subject);
}
```

Bu metod, bütün `Subjects` kolleksiyalarını tək bir siyahıya çevirərək daha effektiv nəticə verir.  

---

## 4. `Select` və `SelectMany` Fərqləri  

| **Xüsusiyyət**           | **Select**                        | **SelectMany**                     |
|--------------------------|----------------------------------|-----------------------------------|
| **Nəticə**               | Kolleksiya                      | Düzləşdirilmiş Kolleksiya        |
| **İstifadə Sahəsi**      | Element projeksiyası            | İç içə kolleksiyaların düzləşdirilməsi |
| **Performans**           | Daha sadə                       | Böyük verilər üçün daha optimal   |

---

## 5. Dinamik İstifadə  

✅ **Nümunə:** Dinamik projeksiya yaratmaq  

```csharp
var subjectsByStudent = students
    .Select(s => new { s.Name, Subjects = s.Subjects })
    .ToList();

foreach (var item in subjectsByStudent)
{
    Console.WriteLine($"{item.Name}: {string.Join(", ", item.Subjects)}");
}
```
