# Emelt szintű digitális kultúra érettségi – SQL és adatbázis-kezelés

# Tartalomjegyzék

1. Mintaadatbázis
2. SQL Syntax
3. Adatbázis-alapfogalmak
4. CREATE DATABASE
5. CREATE TABLE
6. Adattípusok
7. PRIMARY KEY és FOREIGN KEY
8. INSERT INTO
9. SELECT
10. DISTINCT
11. WHERE
12. ORDER BY
13. AND / OR / NOT
14. UPDATE
15. DELETE
16. NULL
17. MIN / MAX
18. COUNT / SUM / AVG
19. LIKE
20. Wildcardok
21. IN
22. BETWEEN
23. Aliasok
24. CONCAT
25. Dátumkezelés
26. CASE
27. JOIN
28. LEFT JOIN
29. GROUP BY
30. HAVING
31. Függvénykombinációk
32. Allekérdezések
33. EXISTS
34. CREATE TABLE AS
35. INSERT INTO SELECT
36. Összetett érettségi feladatok
37. Tipikus hibák

---

# 1. Mintaadatbázis

A teljes tananyag ugyanarra a többtáblás adatbázisra épül.

---

# Kapcsolati séma

```text
sportolo
   |
   | 1:N
   |
jelentkezes
   |
   | N:1
   |
esemeny
   |
   | N:1
   |
sportag
```

---

# Teljes létrehozó script

```sql
DROP DATABASE IF EXISTS sportverseny;

CREATE DATABASE sportverseny
CHARACTER SET utf8mb4
COLLATE utf8mb4_hungarian_ci;

USE sportverseny;

CREATE TABLE sportolo (
    id INT PRIMARY KEY,
    vezeteknev VARCHAR(50) NOT NULL,
    keresztnev VARCHAR(50) NOT NULL,
    varos VARCHAR(50),
    szuletesi_datum DATE,
    email VARCHAR(100)
);

CREATE TABLE sportag (
    id INT PRIMARY KEY,
    nev VARCHAR(50) NOT NULL
);

CREATE TABLE esemeny (
    id INT PRIMARY KEY,
    nev VARCHAR(100) NOT NULL,
    sportag_id INT,
    helyszin VARCHAR(100),
    datum DATE,
    max_letszam INT,
    nevezesi_dij INT,
    FOREIGN KEY (sportag_id)
        REFERENCES sportag(id)
);

CREATE TABLE jelentkezes (
    id INT PRIMARY KEY,
    sportolo_id INT,
    esemeny_id INT,
    pontszam INT,
    fizetett VARCHAR(10),
    megjegyzes VARCHAR(100),
    FOREIGN KEY (sportolo_id)
        REFERENCES sportolo(id),
    FOREIGN KEY (esemeny_id)
        REFERENCES esemeny(id)
);
```

---

# Adatok feltöltése

## sportolo

```sql
INSERT INTO sportolo VALUES
(1, 'Kovács', 'Anna', 'Budapest', '2006-05-12', 'anna@email.hu'),
(2, 'Nagy', 'Béla', 'Pécs', '2005-03-21', 'bela@email.hu'),
(3, 'Tóth', 'Kata', 'Budapest', '2007-08-10', 'kata@email.hu'),
(4, 'Szabó', 'Márk', 'Győr', '2004-11-02', NULL),
(5, 'Varga', 'Lili', 'Pécs', '2006-07-17', 'lili@email.hu'),
(6, 'Kiss', 'Dávid', 'Szeged', '2005-01-25', 'david@email.hu'),
(7, 'Molnár', 'Eszter', 'Debrecen', '2007-09-30', 'eszter@email.hu');
```

---

## sportag

```sql
INSERT INTO sportag VALUES
(1, 'Kosárlabda'),
(2, 'Kézilabda'),
(3, 'Futball'),
(4, 'Röplabda'),
(5, 'Úszás');
```

---

## esemeny

```sql
INSERT INTO esemeny VALUES
(1, 'Tavaszi kupa', 1, 'Budapest Aréna', '2026-03-12', 20, 5000),
(2, 'Városi bajnokság', 2, 'Pécsi Sportcsarnok', '2026-04-03', 16, 4500),
(3, 'Nyári fociest', 3, 'Győri pálya', '2026-06-20', 22, 3000),
(4, 'Röplabda nap', 4, 'Budapest Aréna', '2026-05-15', 18, 3500),
(5, 'Őszi úszónap', 5, 'Debreceni Uszoda', '2026-09-10', 30, 6000);
```

---

## jelentkezes

```sql
INSERT INTO jelentkezes VALUES
(1, 1, 1, 18, 'igen', 'stabil teljesítmény'),
(2, 2, 1, 12, 'nem', 'késői befizetés'),
(3, 3, 2, 20, 'igen', 'kiemelkedő eredmény'),
(4, 4, 3, 9, 'igen', NULL),
(5, 5, 2, 15, 'nem', NULL),
(6, 1, 4, 17, 'igen', 'jó teljesítmény'),
(7, 3, 1, 19, 'igen', 'nagyon jó dobószázalék'),
(8, 6, 3, 14, 'igen', NULL),
(9, 7, 5, 16, 'nem', 'első verseny');
```

---

# 2. SQL Syntax

## Általános syntax

```sql
SELECT oszlopok
FROM tabla
WHERE feltetel
ORDER BY oszlop;
```

---

## Példa

```sql
SELECT vezeteknev, keresztnev
FROM sportolo;
```

### Végeredmény

| vezeteknev | keresztnev |
|---|---|
| Kovács | Anna |
| Nagy | Béla |
| Tóth | Kata |

---

## Bonyolultabb példa

```sql
SELECT vezeteknev,
       keresztnev
FROM sportolo
WHERE varos = 'Budapest'
ORDER BY vezeteknev,
         keresztnev;
```

### Végeredmény

| vezeteknev | keresztnev |
|---|---|
| Kovács | Anna |
| Tóth | Kata |

---

# 3. Adatbázis-alapfogalmak

## Rekord példa

| id | vezeteknev | keresztnev |
|---:|---|---|
| 1 | Kovács | Anna |

---

## PRIMARY KEY példa

```sql
id INT PRIMARY KEY
```

---

## FOREIGN KEY példa

```sql
FOREIGN KEY (sportolo_id)
REFERENCES sportolo(id)
```

---

# 4. CREATE DATABASE

## Példa

```sql
CREATE DATABASE sportverseny;
```

---

# 5. CREATE TABLE

## Példa

```sql
CREATE TABLE sportolo (
    id INT PRIMARY KEY,
    nev VARCHAR(100)
);
```

---

## Bonyolultabb példa

```sql
CREATE TABLE jelentkezes (
    id INT PRIMARY KEY,
    sportolo_id INT,
    esemeny_id INT,
    pontszam INT,
    FOREIGN KEY (sportolo_id)
        REFERENCES sportolo(id),
    FOREIGN KEY (esemeny_id)
        REFERENCES esemeny(id)
);
```

---

# 6. Adattípusok

| Típus | Jelentés |
|---|---|
| INT | egész szám |
| VARCHAR | szöveg |
| DATE | dátum |
| DOUBLE | valós szám |
| BOOLEAN | logikai érték |

---

# 7. PRIMARY KEY és FOREIGN KEY

## PRIMARY KEY

```sql
id INT PRIMARY KEY
```

---

## FOREIGN KEY

```sql
FOREIGN KEY (sportag_id)
REFERENCES sportag(id)
```

---

# 8. INSERT INTO

## Példa

```sql
INSERT INTO sportag
VALUES (6, 'Tenisz');
```

---

## Bonyolultabb példa

```sql
INSERT INTO jelentkezes
VALUES (
    10,
    2,
    4,
    18,
    'igen',
    'utolsó pillanatos nevezés'
);
```

---

# 9. SELECT

## Példa

```sql
SELECT *
FROM sportolo;
```

### Végeredmény

| id | vezeteknev | keresztnev | varos |
|---:|---|---|---|
| 1 | Kovács | Anna | Budapest |
| 2 | Nagy | Béla | Pécs |

---

## Bonyolultabb példa

```sql
SELECT vezeteknev,
       keresztnev,
       varos
FROM sportolo;
```

### Végeredmény

| vezeteknev | keresztnev | varos |
|---|---|---|
| Kovács | Anna | Budapest |
| Nagy | Béla | Pécs |

---

# 10. DISTINCT

## Példa

```sql
SELECT DISTINCT varos
FROM sportolo;
```

### Végeredmény

| varos |
|---|
| Budapest |
| Pécs |
| Győr |
| Szeged |
| Debrecen |

---

## Bonyolultabb példa

```sql
SELECT COUNT(DISTINCT varos)
FROM sportolo;
```

### Végeredmény

| COUNT(DISTINCT varos) |
|---:|
| 5 |

---

# 11. WHERE

## Példa

```sql
SELECT *
FROM sportolo
WHERE varos = 'Budapest';
```

### Végeredmény

| id | vezeteknev | keresztnev |
|---:|---|---|
| 1 | Kovács | Anna |
| 3 | Tóth | Kata |

---

## Bonyolultabb példa

```sql
SELECT vezeteknev,
       keresztnev
FROM sportolo
WHERE YEAR(szuletesi_datum) > 2006;
```

### Végeredmény

| vezeteknev | keresztnev |
|---|---|
| Tóth | Kata |
| Molnár | Eszter |

---

# 12. ORDER BY

## Példa

```sql
SELECT *
FROM sportolo
ORDER BY vezeteknev;
```

### Végeredmény

| vezeteknev | keresztnev |
|---|---|
| Kiss | Dávid |
| Kovács | Anna |

---

## Bonyolultabb példa

```sql
SELECT sportolo_id,
       pontszam
FROM jelentkezes
ORDER BY pontszam DESC
LIMIT 3;
```

### Végeredmény

| sportolo_id | pontszam |
|---:|---:|
| 3 | 20 |
| 3 | 19 |
| 1 | 18 |

---

# 13. AND / OR / NOT

## Példa

```sql
SELECT *
FROM sportolo
WHERE varos = 'Budapest'
AND YEAR(szuletesi_datum) = 2006;
```

### Végeredmény

| vezeteknev | keresztnev |
|---|---|
| Kovács | Anna |

---

## Bonyolultabb példa

```sql
SELECT vezeteknev,
       keresztnev,
       varos
FROM sportolo
WHERE (
    varos = 'Budapest'
    OR varos = 'Pécs'
)
AND YEAR(szuletesi_datum) > 2005;
```

### Végeredmény

| vezeteknev | keresztnev | varos |
|---|---|---|
| Kovács | Anna | Budapest |
| Tóth | Kata | Budapest |
| Varga | Lili | Pécs |

---

# 14. UPDATE

## Példa

```sql
UPDATE sportolo
SET varos = 'Debrecen'
WHERE id = 1;
```

---

## Bonyolultabb példa

```sql
UPDATE jelentkezes
SET fizetett = 'függőben'
WHERE fizetett = 'nem';
```

---

# 15. DELETE

## Példa

```sql
DELETE FROM sportolo
WHERE id = 7;
```

---

## Bonyolultabb példa

```sql
DELETE FROM jelentkezes
WHERE pontszam < 10;
```

---

# 16. NULL

## Példa

```sql
SELECT *
FROM sportolo
WHERE email IS NULL;
```

### Végeredmény

| vezeteknev | keresztnev |
|---|---|
| Szabó | Márk |

---

## Bonyolultabb példa

```sql
SELECT DISTINCT s.vezeteknev,
                s.keresztnev
FROM sportolo s
JOIN jelentkezes j
ON s.id = j.sportolo_id
WHERE s.email IS NULL;
```

### Végeredmény

| vezeteknev | keresztnev |
|---|---|
| Szabó | Márk |

---

# 17. MIN / MAX

## Példa

```sql
SELECT MAX(pontszam)
FROM jelentkezes;
```

### Végeredmény

| MAX(pontszam) |
|---:|
| 20 |

---

## Bonyolultabb példa

```sql
SELECT esemeny_id,
       MAX(pontszam) AS maxpont
FROM jelentkezes
GROUP BY esemeny_id;
```

### Végeredmény

| esemeny_id | maxpont |
|---:|---:|
| 1 | 19 |
| 2 | 20 |

---

# 18. COUNT / SUM / AVG

## Példa

```sql
SELECT AVG(pontszam)
FROM jelentkezes;
```

### Végeredmény

| AVG(pontszam) |
|---:|
| 15.56 |

---

## Bonyolultabb példa

```sql
SELECT sp.nev,
       ROUND(AVG(j.pontszam), 2) AS atlag
FROM sportag sp
JOIN esemeny e
ON sp.id = e.sportag_id
JOIN jelentkezes j
ON e.id = j.esemeny_id
GROUP BY sp.id,
         sp.nev;
```

### Végeredmény

| nev | atlag |
|---|---:|
| Kosárlabda | 16.33 |
| Kézilabda | 17.50 |

---

# 19. LIKE

## Példa

```sql
SELECT *
FROM sportolo
WHERE vezeteknev LIKE 'K%';
```

### Végeredmény

| vezeteknev | keresztnev |
|---|---|
| Kovács | Anna |
| Kiss | Dávid |

---

## Bonyolultabb példa

```sql
SELECT vezeteknev,
       keresztnev
FROM sportolo
WHERE keresztnev LIKE '%a%';
```

### Végeredmény

| vezeteknev | keresztnev |
|---|---|
| Kovács | Anna |
| Tóth | Kata |

---

# 20. Wildcardok

| Jel | Jelentés |
|---|---|
| % | tetszőleges hossz |
| _ | egy karakter |

---

## Példa

```sql
SELECT *
FROM sportolo
WHERE keresztnev LIKE 'A___';
```

### Végeredmény

| keresztnev |
|---|
| Anna |

---

# 21. IN

## Példa

```sql
SELECT *
FROM sportolo
WHERE varos IN ('Budapest', 'Pécs');
```

### Végeredmény

| vezeteknev | keresztnev |
|---|---|
| Kovács | Anna |
| Nagy | Béla |

---

## Bonyolultabb példa

```sql
SELECT nev,
       helyszin
FROM esemeny
WHERE helyszin IN (
    'Budapest Aréna',
    'Győri pálya'
);
```

### Végeredmény

| nev | helyszin |
|---|---|
| Tavaszi kupa | Budapest Aréna |
| Nyári fociest | Győri pálya |

---

# 22. BETWEEN

## Példa

```sql
SELECT *
FROM sportolo
WHERE YEAR(szuletesi_datum)
BETWEEN 2005 AND 2006;
```

### Végeredmény

| vezeteknev | keresztnev |
|---|---|
| Kovács | Anna |
| Nagy | Béla |

---

## Bonyolultabb példa

```sql
SELECT nev,
       datum
FROM esemeny
WHERE datum
BETWEEN '2026-04-01'
AND '2026-06-30';
```

### Végeredmény

| nev | datum |
|---|---|
| Városi bajnokság | 2026-04-03 |
| Röplabda nap | 2026-05-15 |

---

# 23. Aliasok

## Példa

```sql
SELECT vezeteknev AS vezetek
FROM sportolo;
```

### Végeredmény

| vezetek |
|---|
| Kovács |
| Nagy |

---

## Bonyolultabb példa

```sql
SELECT CONCAT(
    vezeteknev,
    ' ',
    keresztnev
) AS teljes_nev
FROM sportolo;
```

### Végeredmény

| teljes_nev |
|---|
| Kovács Anna |
| Nagy Béla |

---

# 24. CONCAT

## Példa

```sql
SELECT CONCAT(
    vezeteknev,
    ' ',
    keresztnev
)
FROM sportolo;
```

### Végeredmény

| CONCAT(...) |
|---|
| Kovács Anna |

---

## Bonyolultabb példa

```sql
SELECT CONCAT(
    vezeteknev,
    ' ',
    keresztnev,
    ' - ',
    varos
) AS adat
FROM sportolo;
```

### Végeredmény

| adat |
|---|
| Kovács Anna - Budapest |

---

# 25. Dátumkezelés

# YEAR()

## Példa

```sql
SELECT YEAR(datum)
FROM esemeny;
```

### Végeredmény

| YEAR(datum) |
|---:|
| 2026 |

---

## Bonyolultabb példa

```sql
SELECT nev,
       datum
FROM esemeny
WHERE YEAR(datum) = 2026;
```

### Végeredmény

| nev | datum |
|---|---|
| Tavaszi kupa | 2026-03-12 |

---

# MONTH()

## Példa

```sql
SELECT MONTH(datum)
FROM esemeny;
```

### Végeredmény

| MONTH(datum) |
|---:|
| 3 |
| 4 |

---

## Bonyolultabb példa

```sql
SELECT nev,
       datum
FROM esemeny
WHERE MONTH(datum) = 5;
```

### Végeredmény

| nev | datum |
|---|---|
| Röplabda nap | 2026-05-15 |

---

# DAY()

## Példa

```sql
SELECT DAY(datum)
FROM esemeny;
```

### Végeredmény

| DAY(datum) |
|---:|
| 12 |
| 3 |

---

## Bonyolultabb példa

```sql
SELECT nev,
       datum
FROM esemeny
WHERE DAY(datum) = 15;
```

### Végeredmény

| nev | datum |
|---|---|
| Röplabda nap | 2026-05-15 |

---

# DATEDIFF()

## Példa

```sql
SELECT DATEDIFF(
    '2026-06-01',
    '2026-05-20'
);
```

### Végeredmény

| DATEDIFF |
|---:|
| 12 |

---

## Bonyolultabb példa

```sql
SELECT nev,
       datum,
       DATEDIFF(
           datum,
           '2026-03-01'
       ) AS napok
FROM esemeny;
```

### Végeredmény

| nev | datum | napok |
|---|---|---:|
| Tavaszi kupa | 2026-03-12 | 11 |

---

# DATE_FORMAT()

## Példa

```sql
SELECT DATE_FORMAT(
    datum,
    '%Y.%m.%d'
)
FROM esemeny;
```

### Végeredmény

| DATE_FORMAT |
|---|
| 2026.03.12 |

---

## Bonyolultabb példa

```sql
SELECT nev,
DATE_FORMAT(
    datum,
    '%Y. %M %d.'
) AS formatalt_datum
FROM esemeny;
```

### Végeredmény

| nev | formatalt_datum |
|---|---|
| Tavaszi kupa | 2026. March 12. |

---

# 26. CASE

## Példa

```sql
SELECT pontszam,
CASE
    WHEN pontszam >= 18
    THEN 'kiváló'
    ELSE 'egyéb'
END AS minosites
FROM jelentkezes;
```

### Végeredmény

| pontszam | minosites |
|---:|---|
| 18 | kiváló |
| 12 | egyéb |

---

## Bonyolultabb példa

```sql
SELECT CONCAT(
    s.vezeteknev,
    ' ',
    s.keresztnev
) AS sportolo,
CASE
    WHEN j.pontszam >= 18
    THEN 'kiváló'
    WHEN j.pontszam >= 14
    THEN 'jó'
    ELSE 'átlagos'
END AS minosites
FROM sportolo s
JOIN jelentkezes j
ON s.id = j.sportolo_id;
```

### Végeredmény

| sportolo | minosites |
|---|---|
| Kovács Anna | kiváló |
| Nagy Béla | átlagos |

---

# 27. JOIN

## Példa

```sql
SELECT s.vezeteknev,
       j.pontszam
FROM sportolo s
JOIN jelentkezes j
ON s.id = j.sportolo_id;
```

### Végeredmény

| vezeteknev | pontszam |
|---|---:|
| Kovács | 18 |
| Nagy | 12 |

---

## Bonyolultabb példa

```sql
SELECT CONCAT(
    s.vezeteknev,
    ' ',
    s.keresztnev
) AS sportolo,
e.nev AS esemeny,
j.pontszam
FROM sportolo s
JOIN jelentkezes j
ON s.id = j.sportolo_id
JOIN esemeny e
ON e.id = j.esemeny_id;
```

### Végeredmény

| sportolo | esemeny | pontszam |
|---|---|---:|
| Kovács Anna | Tavaszi kupa | 18 |
| Tóth Kata | Városi bajnokság | 20 |

---

# 28. LEFT JOIN

## Példa

```sql
SELECT s.vezeteknev,
       j.id
FROM sportolo s
LEFT JOIN jelentkezes j
ON s.id = j.sportolo_id;
```

### Végeredmény

| vezeteknev | id |
|---|---:|
| Kovács | 1 |
| Nagy | 2 |

---

## Bonyolultabb példa

```sql
SELECT s.vezeteknev,
       s.keresztnev
FROM sportolo s
LEFT JOIN jelentkezes j
ON s.id = j.sportolo_id
WHERE j.id IS NULL;
```

### Végeredmény

| vezeteknev | keresztnev |
|---|---|

---

# 29. GROUP BY

## Példa

```sql
SELECT varos,
       COUNT(*)
FROM sportolo
GROUP BY varos;
```

### Végeredmény

| varos | COUNT(*) |
|---|---:|
| Budapest | 2 |
| Pécs | 2 |

---

## Bonyolultabb példa

```sql
SELECT sp.nev,
       COUNT(j.id) AS jelentkezesek
FROM sportag sp
JOIN esemeny e
ON sp.id = e.sportag_id
JOIN jelentkezes j
ON e.id = j.esemeny_id
GROUP BY sp.id,
         sp.nev;
```

### Végeredmény

| nev | jelentkezesek |
|---|---:|
| Kosárlabda | 3 |
| Kézilabda | 2 |

---

# 30. HAVING

## Példa

```sql
SELECT varos,
       COUNT(*)
FROM sportolo
GROUP BY varos
HAVING COUNT(*) >= 2;
```

### Végeredmény

| varos | COUNT(*) |
|---|---:|
| Budapest | 2 |
| Pécs | 2 |

---

## Bonyolultabb példa

```sql
SELECT e.nev,
       COUNT(j.id) AS db
FROM esemeny e
JOIN jelentkezes j
ON e.id = j.esemeny_id
GROUP BY e.id,
         e.nev
HAVING COUNT(j.id) >= 2;
```

### Végeredmény

| nev | db |
|---|---:|
| Tavaszi kupa | 3 |
| Városi bajnokság | 2 |

---

# 31. Függvénykombinációk

## Példa

```sql
SELECT ROUND(
    AVG(pontszam),
    2
)
FROM jelentkezes;
```

### Végeredmény

| ROUND(...) |
|---:|
| 15.56 |

---

## Bonyolultabb példa

```sql
SELECT CONCAT(
    s.vezeteknev,
    ' ',
    s.keresztnev
) AS sportolo,
ROUND(
    AVG(j.pontszam),
    2
) AS atlagpont
FROM sportolo s
JOIN jelentkezes j
ON s.id = j.sportolo_id
GROUP BY s.id,
         s.vezeteknev,
         s.keresztnev;
```

### Végeredmény

| sportolo | atlagpont |
|---|---:|
| Kovács Anna | 17.50 |
| Tóth Kata | 19.50 |

---

# 32. Allekérdezések

## Példa

```sql
SELECT *
FROM jelentkezes
WHERE pontszam = (
    SELECT MAX(pontszam)
    FROM jelentkezes
);
```

### Végeredmény

| id | sportolo_id | pontszam |
|---:|---:|---:|
| 3 | 3 | 20 |

---

## Bonyolultabb példa

```sql
SELECT CONCAT(
    s.vezeteknev,
    ' ',
    s.keresztnev
) AS sportolo,
j.pontszam
FROM sportolo s
JOIN jelentkezes j
ON s.id = j.sportolo_id
WHERE j.pontszam > (
    SELECT AVG(pontszam)
    FROM jelentkezes
);
```

### Végeredmény

| sportolo | pontszam |
|---|---:|
| Kovács Anna | 18 |
| Tóth Kata | 20 |

---

# 33. EXISTS

## Példa

```sql
SELECT *
FROM sportolo s
WHERE EXISTS (
    SELECT 1
    FROM jelentkezes j
    WHERE j.sportolo_id = s.id
);
```

### Végeredmény

| id | vezeteknev |
|---:|---|
| 1 | Kovács |
| 2 | Nagy |

---

## Bonyolultabb példa

```sql
SELECT *
FROM esemeny e
WHERE EXISTS (
    SELECT 1
    FROM jelentkezes j
    WHERE j.esemeny_id = e.id
);
```

### Végeredmény

| id | nev |
|---:|---|
| 1 | Tavaszi kupa |
| 2 | Városi bajnokság |

---

# 34. CREATE TABLE AS

## Példa

```sql
CREATE TABLE budapestiek AS
SELECT *
FROM sportolo
WHERE varos = 'Budapest';
```

---

## Bonyolultabb példa

```sql
CREATE TABLE legjobbak AS
SELECT sportolo_id,
       pontszam
FROM jelentkezes
WHERE pontszam >= 18;
```

---

# 35. INSERT INTO SELECT

## Példa

```sql
INSERT INTO budapestiek
SELECT *
FROM sportolo
WHERE varos = 'Budapest';
```

---

## Bonyolultabb példa

```sql
INSERT INTO legjobbak
SELECT sportolo_id,
       pontszam
FROM jelentkezes
WHERE pontszam > (
    SELECT AVG(pontszam)
    FROM jelentkezes
);
```

---

# 36. Összetett érettségi feladatok

# 1. feladat

```sql
SELECT e.nev AS esemeny,
       CONCAT(
           s.vezeteknev,
           ' ',
           s.keresztnev
       ) AS sportolo,
       j.pontszam
FROM esemeny e
JOIN jelentkezes j
ON e.id = j.esemeny_id
JOIN sportolo s
ON s.id = j.sportolo_id
WHERE j.pontszam = (
    SELECT MAX(j2.pontszam)
    FROM jelentkezes j2
    WHERE j2.esemeny_id = e.id
);
```

### Végeredmény

| esemeny | sportolo | pontszam |
|---|---|---:|
| Tavaszi kupa | Tóth Kata | 19 |
| Városi bajnokság | Tóth Kata | 20 |

---

# 2. feladat

```sql
SELECT sp.nev,
       ROUND(
           AVG(j.pontszam),
           2
       ) AS atlag
FROM sportag sp
JOIN esemeny e
ON sp.id = e.sportag_id
JOIN jelentkezes j
ON e.id = j.esemeny_id
GROUP BY sp.id,
         sp.nev
HAVING AVG(j.pontszam) > 15;
```

### Végeredmény

| nev | atlag |
|---|---:|
| Kosárlabda | 16.33 |
| Kézilabda | 17.50 |

---

# 37. Tipikus hibák

## NULL hibás kezelése

HIBÁS:

```sql
WHERE email = NULL
```

HELYES:

```sql
WHERE email IS NULL
```

---

## COUNT használata WHERE-ben

HIBÁS:

```sql
WHERE COUNT(*) > 2
```

HELYES:

```sql
HAVING COUNT(*) > 2
```

---

## LIKE helyett =

HIBÁS:

```sql
WHERE nev = 'K%'
```

HELYES:

```sql
WHERE nev LIKE 'K%'
```
