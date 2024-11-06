## SQL ÖDEVİ - İSMAİL BERKEHAN GÖKDEMİR

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

**Soru 3)** **hr_departments** tablosu **manager_id** null'dan farklı **department_id**'ler ile **hr_employees** tablosu **department_id**'si null'dan farklı **department_id**'leri tekrar etmeyecek şekilde ekranda gösteriniz. Departman id'leri artan şekilde sıralayınız.

**Cevap 3)**

Sorgu:

```postgresql
SELECT employee_id, CONCAT(first_name, ' ', last_name) AS full_name, job_id
FROM hr.hr_employees
WHERE salary BETWEEN 10 AND 20
```

Çıktı:

![Cevap 3](https://images2.imgbox.com/f2/fa/fh09vY4X_o.jpg)

-----

**Soru 4)** **hr_countries** tablosu, **country_name** alanındaki en kısa uzunluktaki ve en uzun uzunluktaki ülkeleri ayrı ayrı gösteriniz. (Eğer aynı uzunluğa sahipse alfabetik sıraya göre sonucu getiriniz.)

**Cevap 4)**

Sorgu 1) En kısa uzunluktaki:

```postgresql
SELECT country_name
FROM hr.hr_countries
WHERE LENGTH(country_name) = (SELECT MIN(LENGTH(country_name)) FROM hr.hr_countries)
ORDER BY country_name
LIMIT 1
```

Çıktı 1) En kısa uzunluktaki:

![Cevap 4.1](https://images2.imgbox.com/d4/e3/4O3cRODV_o.jpg)

Sorgu 2) En uzun uzunluktaki:

```postgresql
SELECT country_name
FROM hr.hr_countries
WHERE LENGTH(country_name) = (SELECT MAX(LENGTH(country_name)) FROM hr.hr_countries)
```
*1 tane çıktı olduğu için alfabetik sıraya gerek kalmamıştır.*

Çıktı 2) En uzun uzunluktaki:

![Cevap 4.2](https://images2.imgbox.com/4b/ae/x8UBy5Pz_o.jpg)

-----

**Soru 5)** **hr_countries**, **country_name** alanının başında ve sonunda a, e, i, o, u harfleri bulunan ülke isimlerini alfabetik şekilde sıralayın.

**Cevap 5)**

Sorgu:

```postgresql
SELECT country_name
FROM hr.hr_countries
WHERE country_name ~* '^[aeiou].*[aeiou]$'
ORDER BY country_name ASC
```

<ul> 

Sorguda RegEX kullanıldı:

- "~", PostgreSQL'da RegEX eşleştirme operatörüdür.
- "~*" operatorü case-insensitive eşleştirme yapabilmemizi sağlıyor.
- "^" string'in baş harfi ile eşleşmesi durumunu ifade ediyor.
- "[aeiou]", hem küçük hem de büyük harflerdeki tüm ünlüleri içeren bir karakter kümesini belirtiyor.
- ".*" newline hariç herhangi bir karakterle sıfır veya daha fazla kez eşleşmesini belirtiyor.
- "$" string'in son harfi ile eşleşmesi durumunu ifade ediyor.

Çıktı:

![Cevap 5](https://images2.imgbox.com/dd/bf/w7GhoeBP_o.jpg)


-----
