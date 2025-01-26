# Expression-Bodied Members

Expression-bodied members, C# dilində qısa və yığcam kod yazmağa imkan verir. Lakin, yanlış istifadə kodun oxunaqlığını və dəstəklənməsini çətinləşdirə bilər.

---

## 1. Sadə Metodlar Üçün Lazımsız Tam Gövdələr Yazmaq

❌ **Yanlış İstifadə:** Sadə geridönüş əməliyyatlarını tam metod gövdəsi ilə yazmaq.

```csharp
public string GetName()
{
    return "Murat";
}
```

✅ **Düzgün İstifadə:** Expression-bodied members ilə metodları qısaldın.

```csharp
public string GetName() => "Murat";
```

---

## 2. Çox Mürəkkəb İfadələri Expression-Bodied Members ilə Yazmaq

❌ **Yanlış İstifadə:** Çox sətirli ifadələri bir expression-bodied member ilə yazmaq.

```csharp
public string GetFullName(string firstName, string lastName) => 
    $"{firstName} {lastName}".ToUpper() + $" Length: {firstName.Length + lastName.Length}";
```

✅ **Düzgün İstifadə:** Mürəkkəb ifadələri bir neçə sətirli şəkildə açıq yazın.

```csharp
public string GetFullName(string firstName, string lastName)
{
    var fullName = $"{firstName} {lastName}";
    return $"{fullName.ToUpper()} Length: {fullName.Length}";
}
```

---

## 3. Sadə Property-lər üçün Tam Gövdələr Yazmaq

❌ **Yanlış İstifadə:** Property-lər üçün tam metod gövdəsi istifadə etmək.

```csharp
private string _name;

public string Name
{
    get { return _name; }
    set { _name = value; }
}
```

✅ **Düzgün İstifadə:** Expression-bodied members ilə property-ləri qısaldın.

```csharp
public string Name { get; set; }
```

---

## 4. Exception Qaytarma Əməliyyatlarında Expression-Bodied İstifadəsini Yanlış Etmək

❌ **Yanlış İstifadə:** Exception qaytarma əməliyyatlarını expression-bodied member ilə mürəkkəbləşdirmək.

```csharp
public string Name => throw new ArgumentNullException(nameof(Name), "Name is required.");
```

✅ **Düzgün İstifadə:** Exception qaytarma əməliyyatlarını açıq şəkildə yazın.

```csharp
public string Name
{
    get => throw new ArgumentNullException(nameof(Name), "Name is required.");
}
```

---

## 5. Constructor-larda Expression-Bodied İstifadəsini Sui-istifadə Etmək

❌ **Yanlış İstifadə:** Constructor-ları lazımsız yerə expression-bodied tipli yazmaq.

```csharp
public Person(string name) => Name = name ?? throw new ArgumentNullException(nameof(name));
```

✅ **Düzgün İstifadə:** Constructor-larda expression-bodied istifadəsini sadə saxlayın.

```csharp
public Person(string name)
{
    Name = name ?? throw new ArgumentNullException(nameof(name));
}
```

---

## 6. Sadə Əməliyyatlar Üçün Tam Gövdələr Yazmaq

❌ **Yanlış İstifadə:** Sadə property-lər üçün tam metod gövdəsi istifadə etmək.

```csharp
public string Description
{
    get { return "A short description."; }
}
```

✅ **Düzgün İstifadə:** Expression-bodied members ilə sadə əməliyyatları optimallaşdırın.

```csharp
public string Description => "A short description.";
```