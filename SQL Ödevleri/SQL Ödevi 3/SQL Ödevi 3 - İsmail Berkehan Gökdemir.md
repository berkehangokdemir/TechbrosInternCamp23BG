## SQL ÖDEVİ 3 - İSMAİL BERKEHAN GÖKDEMİR

### Sorular & Cevaplar

-----

**Soru 1)** **City** içerisinde 'South' kelimesi geçen dataların **region_name**'ini benzersiz bir şekilde gösterin.

**Cevap 1**)

Sorgu:

```postgresql
SELECT DISTINCT hr.hr_regions.region_name
FROM hr.hr_locations
JOIN hr.hr_countries ON hr.hr_locations.country_id = hr.hr_countries.country_id
JOIN hr.hr_regions ON hr.hr_countries.region_id = hr.hr_regions.region_id
WHERE hr.hr_locations.city LIKE '%South%'
```

Çıktı:

![Cevap 1](https://images2.imgbox.com/ce/6f/1kE2B4Mo_o.jpg)

-----

**Soru 2)** **manager_id**'si 100 olan yöneticinin altında çalışan elemanlardan, en yüksek maaş alan kimdir? Eğer birden fazla en yüksek maaş alan varsa alfabetik olarak sıralayıp ilk geleni alınız.

**Cevap 2)**

Sorgu:

```postgresql
SELECT employee_id, CONCAT(first_name, ' ', last_name) AS full_name,
job_id, salary, manager_id
FROM hr_employees
WHERE manager_id = 100
ORDER BY salary DESC, full_name ASC
LIMIT 1
```

Çıktı:

![Cevap 2](https://images2.imgbox.com/c2/1a/Y48p6Thi_o.jpg)

-----

**Soru 3)** **hr.hr_employees** tablosunda, **hire_date**'i 1985 ve 1995 arasındaki kayıtların bağlı oldukları departman isimleri nelerdir?

**Cevap 3)**

Sorgu:

```postgresql
SELECT DISTINCT hr.hr_departments.department_name
FROM hr.hr_employees
JOIN hr.hr_departments ON hr.hr_employees.department_id = hr.hr_departments.department_id
WHERE hr.hr_employees.hire_date >= '1985-01-01' AND hr.hr_employees.hire_date <= '1994-12-31'
```

Çıktı:

![Cevap 3](https://images2.imgbox.com/fa/60/76FV7oa9_o.jpg)

-----

**Soru 4)** **tbl.adsoyad** olacak şekilde tablolar yaratın. Bu tablolardaki verileri **hr** şemasından çekin. Çektiğiniz verilere uygun olarak CREATE TABLE'ınızı detaylandırın (NOT NULL, UNIQUE, FK, PK, CHECK, tabloya yorum ve kolona yorum gibi).
NOT: Gördüğümüz her özelliği bir kez kullanacak şekilde aktarım yapacağınız dataları belirlemeniz sizler için iyi olacaktır.

**Cevap 4)**

**1.Adım - Tablo oluşturma sorgusu:**

```postgresql
CREATE TABLE berkehangokdemir (
employee_id INTEGER PRIMARY KEY NOT NULL UNIQUE,
first_name VARCHAR(20),
last_name VARCHAR(20),
job_title VARCHAR(50),
salary INTEGER
)
```

**1.Adım - Sonuç:**

![Cevap 4.1](https://images2.imgbox.com/9c/dd/Ykb0VBWr_o.jpg)

**2.Adım - Veri çekme sorguları:**

**2.1** - **hr.hr_employees** tarafından çekilenler:

```postgresql
INSERT INTO berkehangokdemir (
employee_id,
first_name,
last_name
)
SELECT employee_id, first_name, last_name
FROM hr.hr_employees
```

**2.1 - Sonuç:**

![Cevap 4.2.1](https://images2.imgbox.com/00/62/PEqGgEOK_o.jpg)

**2.2** - **hr.hr_jobs** tarafından çekilenler:

```postgresql
UPDATE berkehangokdemir
SET job_title = hr.hr_jobs.job_title
FROM hr.hr_employees
JOIN hr.hr_jobs ON hr.hr_employees.job_id = hr.hr_jobs.job_id
WHERE berkehangokdemir.employee_id = hr.hr_employees.employee_id
```

**2.2 - Sonuç:**

![Cevap 4.2.2.1](https://images2.imgbox.com/6a/85/ocSiNhgs_o.jpg)

![Cevap 4.2.2.2](https://images2.imgbox.com/c2/95/O7k8MzXa_o.jpg)

**2.3** - **hr.hr_employees** tarafından **salary**:

```postgresql
UPDATE berkehangokdemir
SET salary = hr.hr_employees.salary
FROM hr.hr_employees
WHERE berkehangokdemir.employee_id = hr.hr_employees.employee_id
```

![Cevap 4.2.3.1](https://images2.imgbox.com/02/da/sp9xdkqP_o.jpg)

![Cevap 4.2.3.2](https://images2.imgbox.com/a9/e4/mNvJFqJG_o.jpg)

**3.Adım - Tablo düzeltme & ilişkili kolon ekleme sorguları**

**3.1** - **currently_active** kolonu eklenmesi & veri transferi:

**1.Sorgu:**

```postgresql
ALTER TABLE berkehangokdemir
ADD currently_active BOOLEAN
```

**Sonuçlar:**

![Cevap 4.3.1.1](https://images2.imgbox.com/d3/41/N0BXKdYB_o.jpg)

![Cevap 4.3.1.1](https://images2.imgbox.com/0e/1f/0mW806BE_o.jpg)

**2.Sorgu:**

```postgresql
UPDATE berkehangokdemir
SET currently_active = CASE
  WHEN EXISTS (
    SELECT 1
    FROM hr.hr_job_history
    WHERE hr_job_history.employee_id = berkehangokdemir.employee_id
      AND hr_job_history.end_date IS NULL
  ) THEN TRUE
  ELSE FALSE
  END
WHERE employee_id IN (
  SELECT employee_id
  FROM hr.hr_job_history
)
```

**Sonuçlar:**

![Cevap 4.3.2.1](https://images2.imgbox.com/41/7c/6TKmlWBs_o.jpg)

![Cevap 4.3.2.2](https://images2.imgbox.com/6c/5a/T5eoyZ5Y_o.jpg)

*Bu aşamada sorgu yaptığımda '[ ]' şeklinde gözüken değerler false çıkmaktadır. Kalan değerler NULL olarak kaldığı için bir update daha yapacağım:*

**3.Sorgu:**

```postgresql
UPDATE berkehangokdemir
SET currently_active = TRUE
WHERE currently_active IS NULL
```

**Sonuçlar:**

![Cevap 4.3.3.1](https://images2.imgbox.com/32/f8/AyH5OeKB_o.jpg)

![Cevap 4.3.3.2](https://images2.imgbox.com/e6/c6/MmFjCkO0_o.jpg)

**3.2** - **years_of_work** kolonu eklenmesi & veri transferi

**1.Sorgu:**

```postgresql
ALTER TABLE berkehangokdemir
ADD years_of_work INTEGER
```

**Sonuçlar:**

![Cevap 4.4.1.1](https://images2.imgbox.com/bb/b6/CfUrKLwo_o.jpg)

![Cevap 4.4.2.1](https://images2.imgbox.com/fe/85/5wAABzsJ_o.jpg)

**2.Sorgu:**

```postgresql
UPDATE berkehangokdemir
SET years_of_work = 
  CASE
    WHEN hr.hr_job_history.end_date IS NOT NULL
    THEN
    DATE_PART('year', hr.hr_job_history.end_date)
    - DATE_PART('year', hr.hr_job_history.start_date)
    ELSE DATE_PART('year', CURRENT_DATE)
    - DATE_PART('year', hr.hr_job_history.start_date)
  END
FROM hr.hr_job_history
WHERE hr.hr_job_history.employee_id = berkehangokdemir.employee_id
```

**Sonuçlar:**

![Cevap 4.4.1.1](https://images2.imgbox.com/11/75/1EC69yhy_o.jpg)

![Cevap 4.4.2.1](https://images2.imgbox.com/e2/dd/E8nvIcGB_o.jpg)

*Kalan değerler NULL olarak kaldığı için bir update daha yapacağım:*

```postgresql
UPDATE berkehangokdemir
SET years_of_work = DATE_PART('year', CURRENT_DATE)
- DATE_PART('year', hr.hr_employees.hire_date)
FROM hr.hr_employees
WHERE hr.hr_employees.employee_id = berkehangokdemir.employee_id
  AND berkehangokdemir.currently_active = TRUE
```

*Varsayım olarak bu veritabanını günümüzde güncel olarak düşünerek yaptım. Dolayısıyla rakamlar büyük.*

**Sonuçlar:**

![Cevap 4.4.1.1](https://images2.imgbox.com/0c/ce/gKy8i4S0_o.jpg)

![Cevap 4.4.2.1](https://images2.imgbox.com/cd/a9/WwENEf80_o.jpg)



-----

**Soru 5)** Dataset'i analiz ederek daha kolay görmek istediğiniz, anlam çıkarabileceğinizi düşündüğünüz sorguları view şeması içerisinde oluşturun.

**Cevap 5)**

**1.Sorgu:**

```postgresql
CREATE VIEW hr.country_id_region_view AS
SELECT
hr.hr_countries.country_id,
hr.hr_countries.country_name,
hr.hr_regions.region_name
FROM hr.hr_countries
JOIN hr.hr_regions
ON hr.hr_countries.region_id = hr.hr_regions.region_id
```

**1.Çıktı:**

![Cevap 5.1](https://images2.imgbox.com/04/5d/HuChVu1L_o.jpg)

**2.Sorgu:**

```postgresql
SELECT * FROM hr.country_id_region_view
```

**2.Çıktı:**

![Cevap 5.2](https://images2.imgbox.com/b9/70/i2uNy9a8_o.jpg)



-----

**Soru 6)** Herkes kendi ad soyadını kullanacak şekilde bir kullanıcı yaratsın Postgres DB'de ve bütün ayrıcalıkları kod kullanarak versin.

**Cevap 6)**

Sorgu:

```postgresql
CREATE USER berkehangokdemir WITH PASSWORD 'techbros2023'

GRANT ALL PRIVILEGES ON DATABASE postgres to berkehangokdemir

ALTER ROLE berkehangokdemir SUPERUSER CREATEDB
CREATEROLE INHERIT LOGIN REPLICATION BYPASSRLS

GRANT ALL ON SCHEMA hr TO berkehangokdemir

GRANT CREATE ON SCHEMA hr TO berkehangokdemir
```

Çıktı:

![Cevap 6](https://images2.imgbox.com/11/5e/BzsZlttd_o.jpg)

-----
