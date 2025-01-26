# Query Parametrləri İstifadəsi

Dapper, SQL sorğularında parametr istifadəsi ilə həm təhlükəsiz, həm də oxunaqlı kod yazmağınızı təmin edir. Parametrlər sayəsində SQL injeksiyonunun qarşısını ala bilər və performansı artıra bilərsiniz. Lakin, parametrlərin yanlış istifadəsi performans itkisinə və təhlükəsizlik açıqlarına səbəb ola bilər.

---

## 1. Parametrsiz Sorğular Yazmaq

❌ **Yanlış İstifadə:** Parametrsiz sorğular SQL injeksiyonuna açıq qapı buraxır.

```csharp
var query = $"SELECT * FROM Users WHERE Name = '{name}'";
var users = connection.Query<User>(query);
```

✅ **Düzgün İstifadə:** Parametrlər ilə təhlükəsiz sorğular yazın.

```csharp
var query = "SELECT * FROM Users WHERE Name = @Name";
var users = connection.Query<User>(query, new { Name = name });
```

---

## 2. Birdən Çox Parametr İstifadəsi

❌ **Yanlış İstifadə:** Parametrləri əllə birləşdirmək.

```csharp
var query = $"SELECT * FROM Users WHERE Name = '{name}' AND Age = {age}";
var users = connection.Query<User>(query);
```

✅ **Düzgün İstifadə:** Dapper-in parametr idarəetməsini istifadə edin.

```csharp
var query = "SELECT * FROM Users WHERE Name = @Name AND Age = @Age";
var users = connection.Query<User>(query, new { Name = name, Age = age });
```

---

## 3. Dinamik Parametrlərlə İşləmək

Dapper, dinamik parametrlər ilə çevik sorğular yazmağınıza imkan verir.

✅ **Nümunə:**

```csharp
var parameters = new DynamicParameters();
parameters.Add("Name", name);
parameters.Add("Age", age);

var query = "SELECT * FROM Users WHERE Name = @Name AND Age = @Age";
var users = connection.Query<User>(query, parameters);
```

---

## 4. Performans və Təhlükəsizlik Məsləhətləri

- **SQL İnjeksiyonunun Qarşısını Alın:** Hər zaman parametrli sorğuları istifadə edin.
- **DynamicParameters İstifadəsi:** Dinamik ssenarilərdə parametr idarəetməsini asanlaşdırır.
- **Parametrlərin Növlərinə Diqqət Edin:** SQL tipləri ilə uyğun parametrlər istifadə edin.

```csharp
var parameters = new DynamicParameters();
parameters.Add("IsActive", true, DbType.Boolean);
parameters.Add("JoinDate", DateTime.Now, DbType.DateTime);

var query = "SELECT * FROM Users WHERE IsActive = @IsActive AND JoinDate > @JoinDate";
var users = connection.Query<User>(query, parameters);
```
