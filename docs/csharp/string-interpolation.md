# String İnterpolasiyası

String interpolasiyası, mətn və dəyişənləri birləşdirmək üçün təsirli bir üsul təqdim edir. Bu xüsusiyyət, kodunuzu daha oxunaqlı və qısa hala gətirə bilər. Lakin, yanlış istifadələri performans problemlərinə və oxunaqlılıq çətinliklərinə yol aça bilər.

---

## 1. Mürəkkəb İfadələri String İnterpolasiyasında İstifadə Etmək

❌ **Yanlış İstifadə:** String interpolasiya içində mürəkkəb ifadələr istifadə etmək.

```csharp
var name = "Murat";
var greeting = $"Merhaba, {name.ToUpper() + "!"} It is {DateTime.Now.ToString("HH:mm:ss")}";
Console.WriteLine(greeting);
```

✅ **Düzgün İstifadə:** Mürəkkəb ifadələri interpolasiya xaricində işləyin.

```csharp
var name = "Murat".ToUpper();
var time = DateTime.Now.ToString("HH:mm:ss");
var greeting = $"Merhaba, {name}! It is {time}";
Console.WriteLine(greeting);
```

---

## 2. Lazımsız String.Format İstifadəsi

❌ **Yanlış İstifadə:** String interpolasiya əvəzinə lazımsız `string.Format` istifadəsi.

```csharp
var name = "Murat";
var age = 33;
var message = string.Format("Ad: {0}, Yaş: {1}", name, age);
Console.WriteLine(message);
```

✅ **Düzgün İstifadə:** String interpolasiya ilə daha təmiz bir quruluş əldə edin.

```csharp
var name = "Murat";
var age = 33;
var message = $"Ad: {name}, Yaş: {age}";
Console.WriteLine(message);
```

---

## 3. Performansı Nəzərə Almamaq

❌ **Yanlış İstifadə:** Böyük dövrlərdə string interpolasiya istifadə edərək performansı nəzərə almamaq.

```csharp
for (int i = 0; i < 1000; i++)
{
    var message = $"Current value is: {i}";
    Console.WriteLine(message);
}
```

✅ **Düzgün İstifadə:** StringBuilder kimi performans dostu həllər istifadə edin.

```csharp
var builder = new StringBuilder();
for (int i = 0; i < 1000; i++)
{
    builder.AppendLine($"Current value is: {i}");
}
Console.WriteLine(builder.ToString());
```

---

## 4. Mədəniyyət Fərqliliklərini Nəzərə Almamaq

❌ **Yanlış İstifadə:** String interpolasiyada mədəniyyət fərqliliklərini nəzərə almamaq.

```csharp
var price = 1234.56;
var message = $"Price: {price}";
Console.WriteLine(message); // Fərqli mədəniyyətlərdə yanlış formatda göstərilə bilər
```

✅ **Düzgün İstifadə:** Müəyyən bir mədəniyyəti açıq şəkildə müəyyənləşdirərək formatlayın.

```csharp
var price = 1234.56;
var message = $"Price: {price.ToString("C", CultureInfo.GetCultureInfo("en-US"))}";
Console.WriteLine(message);
```

---

## 5. Çox Sətirli String İçində Yanlış İstifadə

❌ **Yanlış İstifadə:** Çox sətirli stringlərdə string interpolasiyanı nizamsız istifadə etmək.

```csharp
var name = "Murat";
var message = $"Merhaba, {name}
Hosgeldin!";
Console.WriteLine(message);
```

✅ **Düzgün İstifadə:** Çox sətirli stringlərdə nizamlı bir quruluş təmin edin.

```csharp
var name = "Murat";
var message = $"Merhaba, {name}
Hosgeldin!";
Console.WriteLine(message);
```

---

## 6. Lazımsız Mörtərizə İstifadəsi

❌ **Yanlış İstifadə:** İnterpolasiya ifadələrində lazımsız mörtərizələr əlavə etmək.

```csharp
var name = "Murat";
var message = $"Merhaba, {(name)}!";
Console.WriteLine(message);
```

✅ **Düzgün İstifadə:** Lazımsız mörtərizələrdən qaçının.

```csharp
var name = "Murat";
var message = $"Merhaba, {name}!";
Console.WriteLine(message);
```
