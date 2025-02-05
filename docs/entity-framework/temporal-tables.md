# Müvəqqəti Cədvəllər (Temporal Tables)

Müvəqqəti cədvəllər, verilənlər bazası cədvəllərindəki dəyişikliklərin tarixini tutaraq verilən versiyalamağı mümkün edir. Bu xüsusiyyət, səhv ayırd etmə, verilənlər analitikası və qanuni tələblər üçün olduqca faydalıdır. Lakin, yanlış istifadələri saxlama və performans problemlərinə səbəb ola bilər.

---

## 1. Müvəqqəti Cədvəlləri Gərəksiz İstifadə Etmək

❌ **Yanlış İstifadə:** Keçmiş verilənlərə ehtiyac duymayan cədvəllər üçün müvəqqəti cədvəllər istifadə etmək.

```sql
CREATE TABLE Products
(
    ProductId INT PRIMARY KEY,
    Name NVARCHAR(100),
    Price DECIMAL(10, 2),
    SysStartTime DATETIME2 GENERATED ALWAYS AS ROW START,
    SysEndTime DATETIME2 GENERATED ALWAYS AS ROW END,
    PERIOD FOR SYSTEM_TIME (SysStartTime, SysEndTime)
) WITH (SYSTEM_VERSIONING = ON);
```

✅ **Düzgün İstifadə:** Sadəcə keçmiş verilənlərin kritik olduğu cədvəllər üçün müvəqqəti cədvəlləri aktivləşdirin.

```sql
CREATE TABLE Orders
(
    OrderId INT PRIMARY KEY,
    CustomerId INT,
    OrderDate DATETIME2,
    SysStartTime DATETIME2 GENERATED ALWAYS AS ROW START,
    SysEndTime DATETIME2 GENERATED ALWAYS AS ROW END,
    PERIOD FOR SYSTEM_TIME (SysStartTime, SysEndTime)
) WITH (SYSTEM_VERSIONING = ON);
```

---

## 2. Müvəqqəti Cədvəlin Məlumat İdarəetməsini Göz Ardı Etmək

❌ **Yanlış İstifadə:** Müvəqqəti cədvəllər məlumatlarını avtomatik olaraq təmizləməmək.

```sql
-- Bütün keçmiş verilənlər sonsuza qədər saxlanılır
SELECT * FROM Orders FOR SYSTEM_TIME ALL;
```

✅ **Düzgün İstifadə:** Verilənlər bazası həcmini idarə etmək üçün keçmiş verilənləri təmizləyin.

```sql
ALTER DATABASE [YourDatabase] SET TEMPORAL_HISTORY_RETENTION_PERIOD = 6 MONTHS;
```

---

## 3. Yanlış Sorğu Yazımı

❌ **Yanlış İstifadə:** Müvəqqəti cədvəllər üçün standart sorğuları istifadə etmək.

```sql
SELECT * FROM Orders WHERE OrderDate = '2023-01-01';
```

✅ **Düzgün İstifadə:** Müvəqqəti cədvəllər sorğularında keçmiş verilənləri açıq şəkildə göstərin.

```sql
SELECT * FROM Orders
FOR SYSTEM_TIME AS OF '2023-01-01T00:00:00';
```

---

## 4. Müvəqqəti Cədvəllərlə Yanlış Əlaqələr Qurmaq

❌ **Yanlış İstifadə:** Müvəqqəti cədvəl ehtiva edən cədvəlləri yanlış əlaqələrlə bağlamaq.

```sql
CREATE TABLE OrderDetails
(
    OrderDetailId INT PRIMARY KEY,
    OrderId INT,
    ProductId INT
);
```


✅ **Düzgün İstifadə:** Əlaqələri müvəqqəti cədvəllərə uyğun şəkildə dizayn edin.

```sql
CREATE TABLE OrderDetails
(
    OrderDetailId INT PRIMARY KEY,
    OrderId INT,
    ProductId INT,
    SysStartTime DATETIME2 GENERATED ALWAYS AS ROW START,
    SysEndTime DATETIME2 GENERATED ALWAYS AS ROW END,
    PERIOD FOR SYSTEM_TIME (SysStartTime, SysEndTime)
) WITH (SYSTEM_VERSIONING = ON);
```

---

## 5. Səhv Ayırd Etmə və Analiz Üçün Müvəqqəti Cədvəlləri İstifadə Etməmək

❌ **Yanlış İstifadə:** Müvəqqəti cədvəlləri səhv ayırd etmə və ya verilənlər analitikası üçün istifadə etməmək.

```sql
-- Keçmiş verilənlər sorğulanmır
SELECT * FROM Orders WHERE OrderId = 101;
```

✅ **Düzgün İstifadə:** Müvəqqəti cədvəlləri keçmiş verilənləri analiz etmək üçün aktiv istifadə edin.

```sql
SELECT * FROM Orders
FOR SYSTEM_TIME BETWEEN '2023-01-01T00:00:00' AND '2023-01-31T23:59:59';
```

---

## 6. Performans və Saxlama Xərclərini Göz Ardı Etmək

❌ **Yanlış İstifadə:** Müvəqqəti cədvəllər verilənlər bazası həcmini idarə etməmək.

```sql
-- Bütün keçmiş verilənlər davamlı saxlanılır
SELECT * FROM Orders FOR SYSTEM_TIME ALL;
```

✅ **Düzgün İstifadə:** Performansı optimallaşdırmaq üçün indekslər və arxivləmə istifadə edin.

```sql
CREATE INDEX IX_Orders_SysStartTime ON Orders (SysStartTime);
```
