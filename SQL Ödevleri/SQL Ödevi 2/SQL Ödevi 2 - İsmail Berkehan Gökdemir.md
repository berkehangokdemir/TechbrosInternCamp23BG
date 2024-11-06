## SQL ÖDEVİ 2 - İSMAİL BERKEHAN GÖKDEMİR

### Sorular & Cevaplar

-----

**Soru 1)** **hr_locations** tablosu ile **hr_countries** ve **hr_regions** tablolarını joinleyerek ekrana **location_id**, **city**, **state_province**, **country_name**, **region_name** kolonlarını yansıtınız.

**Cevap 1**)

Sorgu:

```postgresql
SELECT to_char(hr.hr_locations.location_id, '0.000') AS location_id ,
hr.hr_locations.city,
hr.hr_locations.state_province,
hr.hr_countries.country_name, hr.hr_regions.region_name
FROM hr.hr_locations
LEFT JOIN hr.hr_countries ON hr.hr_locations.country_id = hr.hr_countries.country_id
LEFT JOIN hr.hr_regions ON hr.hr_countries.region_id = hr.hr_regions.region_id
```

Çıktı:

![Cevap 1](https://images2.imgbox.com/75/ca/1hTKLtDz_o.jpg)

-----

**Soru 2)** **hr_locations** tablosundaki **state_province** alanındaki null olan dataların **employee_id**, **full_name**, **street_address** ve **city** bilgilerini ekrana getiriniz. (Departman id'si NULL'dan farklı olan datalar için geçerlidir.)

**Cevap 2)**

Sorgu:

```postgresql
SELECT
hr.hr_employees.employee_id,
hr.hr_employees.first_name || ' ' || hr.hr_employees.last_name AS full_name,
hr.hr_locations.street_address,
hr.hr_locations.city
FROM hr.hr_locations
JOIN hr.hr_departments ON hr.hr_locations.location_id = hr.hr_departments.location_id
JOIN hr.hr_employees ON hr.hr_departments.department_id = hr.hr_employees.department_id
WHERE hr.hr_locations.state_province IS NULL AND hr.hr_departments.department_id IS NOT NULL
```

Çıktı:

![Cevap 2](https://images2.imgbox.com/d7/e9/prfYht9Q_o.jpg)

-----

**Soru 3)** **hr_departments** tablosu **manager_id** null'dan farklı **department_id**'ler ile **hr_employees** tablosu **department_id**'si null'dan farklı **department_id**'leri tekrar etmeyecek şekilde ekranda gösteriniz. Departman id'leri artan şekilde sıralayınız. (Tek query ile yapılmalıdır.)

**Cevap 3)**

Sorgu:

```postgresql
SELECT DISTINCT hr.hr_departments.department_id
FROM hr.hr_departments
JOIN hr.hr_employees ON hr.hr_employees.department_id = hr.hr_departments.department_id
WHERE 
(hr.hr_employees.department_id IS NOT NULL) 
AND 
(hr.hr_departments.department_id IS NOT NULL)
ORDER BY hr.hr_departments.department_id
```

Çıktı:

![Cevap 3](https://images2.imgbox.com/5b/62/SCa5CetN_o.jpg)

-----

**Soru 4)** **hr.hr_departments** tablosunda, Finance ve Marketing alanında çalışan çalışanların ortalama ücreti ne kadardır? (Virgülden sonra yalnızca 3 basamak alınız.)

**Cevap 4)**

Sorgu:

```postgresql
SELECT AVG(hr.hr_employees.salary)::numeric(10, 3) AS ort_ucret
FROM hr.hr_departments
JOIN hr.hr_employees ON hr.hr_departments.department_id = hr.hr_employees.department_id
WHERE hr.hr_departments.department_name IN ('Finance', 'Marketing')
```

Çıktı:

![Cevap 4](https://images2.imgbox.com/57/d6/LtgHFRHJ_o.jpg)

-----
