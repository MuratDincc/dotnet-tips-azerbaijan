# Validation və FluentValidation İstifadəsi

Validation (doğrulama), istifadəçi girişlərini yoxlayaraq tətbiqin təhlükəsizliyini və bütövlüyünü təmin etmək üçün kritik bir prosesdir. FluentValidation, güclü və elastik doğrulama qaydaları yaratmağı asanlaşdırır. Yanlış istifadə olunan doğrulama qaydaları, verilənlər uyğunsuzluğuna və təhlükəsizlik boşluqlarına yol aça bilər.

---

## 1. Birbaşa Kodda Validation Qaydaları Yazmaq

❌ **Yanlış İstifadə:** Doğrulama qaydalarını birbaşa iş məntiqinə daxil etmək.

```csharp
public IActionResult CreateUser(User user)
{
    if (string.IsNullOrWhiteSpace(user.Name) || user.Age < 18)
    {
        return BadRequest("Səhv istifadəçi verilənləri.");
    }

    // İş məntiqi
    return Ok();
}
```

✅ **Düzgün İstifadə:** FluentValidation ilə doğrulama qaydalarını ayırın.

```csharp
public class UserValidator : AbstractValidator<User>
{
    public UserValidator()
    {
        RuleFor(user => user.Name).NotEmpty().WithMessage("İstifadəçi adı boş ola bilməz.");
        RuleFor(user => user.Age).GreaterThanOrEqualTo(18).WithMessage("Yaş 18-dən böyük olmalıdır.");
    }
}

public IActionResult CreateUser(User user)
{
    var validator = new UserValidator();
    var result = validator.Validate(user);
    if (!result.IsValid)
    {
        return BadRequest(result.Errors);
    }

    // İş məntiqi
    return Ok();
}
```

---

## 2. Çox Sayda Qaydaya Sahib Mürəkkəb Validatorlar

❌ **Yanlış İstifadə:** Tək bir sinifdə həddindən artıq mürəkkəb doğrulama qaydaları təyin etmək.

```csharp
public class ComplexValidator : AbstractValidator<ComplexObject>
{
    public ComplexValidator()
    {
        RuleFor(x => x.Property1).NotEmpty();
        RuleFor(x => x.Property2).GreaterThan(0);
        RuleFor(x => x.NestedObject.Property).NotEmpty();
        // ... daha çox mürəkkəb qayda
    }
}
```

✅ **Düzgün İstifadə:** Qaydaları alt validatorlara ayıraraq idarə oluna bilən hala gətirin.

```csharp
public class NestedObjectValidator : AbstractValidator<NestedObject>
{
    public NestedObjectValidator()
    {
        RuleFor(x => x.Property).NotEmpty();
    }
}

public class ComplexValidator : AbstractValidator<ComplexObject>
{
    public ComplexValidator()
    {
        RuleFor(x => x.Property1).NotEmpty();
        RuleFor(x => x.Property2).GreaterThan(0);
        RuleFor(x => x.NestedObject).SetValidator(new NestedObjectValidator());
    }
}
```

---

## 3. `CascadeMode` İstifadəsini Nəzərə Almamaq

❌ **Yanlış İstifadə:** Hər qaydanın ayrı-ayrılıqda işə düşməsinə səbəb olmaq.

```csharp
public UserValidator()
{
    RuleFor(user => user.Email).NotEmpty().EmailAddress();
}
```

✅ **Düzgün İstifadə:** CascadeMode istifadə edərək doğrulama prosesini optimallaşdırın.

```csharp
public UserValidator()
{
    RuleFor(user => user.Email)
        .Cascade(CascadeMode.Stop)
        .NotEmpty()
        .EmailAddress();
}
```

---

## 4. Xətaları Anlaşılan Şəkildə Çevirməmək

❌ **Yanlış İstifadə:** Xətaları anlaşılmayan bir formatda qaytarmaq.

```csharp
return BadRequest(result.Errors);
```

✅ **Düzgün İstifadə:** Xətaları istifadəçi dostu bir formatda çevirin.

```csharp
return BadRequest(result.Errors.Select(e => e.ErrorMessage));
```

---

## 5. Global Doğrulama İdarəetməsi İstifadəsini Keçmək

❌ **Yanlış İstifadə:** Hər yerdə manual olaraq validator çağırmaq.

```csharp
var validator = new UserValidator();
var result = validator.Validate(user);
```

✅ **Düzgün İstifadə:** Global doğrulama idarəetməsini aktivləşdirin.

```csharp
services.AddFluentValidationAutoValidation()
        .AddValidatorsFromAssemblyContaining<UserValidator>();
```

---

## 6. Verilənlər Bazası Asılılıqlarını Validatorda İstifadə Etmək

❌ **Yanlış İstifadə:** Validator içində birbaşa verilənlər bazası sorğuları etmək.

```csharp
RuleFor(user => user.Email).Must(email => !dbContext.Users.Any(u => u.Email == email))
    .WithMessage("Bu e-poçt artıq istifadə olunur.");
```

✅ **Düzgün İstifadə:** Asılılıqları constructor vasitəsilə inject edin.

```csharp
public class UserValidator : AbstractValidator<User>
{
    private readonly MyDbContext _dbContext;

    public UserValidator(MyDbContext dbContext)
    {
        _dbContext = dbContext;

        RuleFor(user => user.Email).Must(IsUniqueEmail)
            .WithMessage("Bu e-poçt artıq istifadə olunur.");
    }

    private bool IsUniqueEmail(string email)
    {
        return !_dbContext.Users.Any(u => u.Email == email);
    }
}
```
