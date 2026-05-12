# Emelt szintű digitális kultúra érettségi – SQL és adatbázis-kezelés

## Bevezetés

Ez a tananyag az emelt szintű digitális kultúra érettségi adatbázis-kezelés részéhez készült. A cél az, hogy egy konkrét, többtáblás adatbázison keresztül lásd, hogyan épülnek fel az SQL-lekérdezések, milyen eredménytáblát adnak vissza, és milyen gondolkodással lehet az érettségi feladatokat megoldani.

A tananyag MySQL/MariaDB szemléletű, ezért jól használható phpMyAdmin vagy XAMPP környezetben.

---

## Tartalomjegyzék

1. Mintaadatbázis és létrehozó script  
2. Adatbázis-alapfogalmak  
3. SQL syntax  
4. CREATE DATABASE és DROP DATABASE  
5. CREATE TABLE és DROP TABLE  
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
16. NULL értékek  
17. MIN és MAX  
18. COUNT, SUM és AVG  
19. LIKE és wildcardok  
20. IN  
21. BETWEEN  
22. Aliasok  
23. CONCAT és számított mezők  
24. Dátumkezelés  
25. CASE  
26. JOIN  
27. LEFT JOIN  
28. GROUP BY  
29. HAVING  
30. Függvénykombinációk  
31. Allekérdezések  
32. EXISTS  
33. CREATE TABLE AS  
34. INSERT INTO SELECT  
35. Összetett érettségi feladatok  
36. Tipikus érettségi hibák  

---

# 1. Mintaadatbázis és létrehozó script

A teljes tananyagban egy sportverseny-adatbázist használunk. A példában sportolók jelentkezhetnek sporteseményekre. Egy esemény egy sportághoz tartozik, a jelentkezés pedig összeköti a sportolót az eseménnyel.

## Kapcsolati séma

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

## Teljes létrehozó script

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
    FOREIGN KEY (sportag_id) REFERENCES sportag(id)
);

CREATE TABLE jelentkezes (
    id INT PRIMARY KEY,
    sportolo_id INT,
    esemeny_id INT,
    pontszam INT,
    fizetett VARCHAR(10),
    megjegyzes VARCHAR(100),
    FOREIGN KEY (sportolo_id) REFERENCES sportolo(id),
    FOREIGN KEY (esemeny_id) REFERENCES esemeny(id)
);

INSERT INTO sportolo VALUES
(1, 'Kovács', 'Anna', 'Budapest', '2006-05-12', 'anna@email.hu'),
(2, 'Nagy', 'Béla', 'Pécs', '2005-03-21', 'bela@email.hu'),
(3, 'Tóth', 'Kata', 'Budapest', '2007-08-10', 'kata@email.hu'),
(4, 'Szabó', 'Márk', 'Győr', '2004-11-02', NULL),
(5, 'Varga', 'Lili', 'Pécs', '2006-07-17', 'lili@email.hu'),
(6, 'Kiss', 'Dávid', 'Szeged', '2005-01-25', 'david@email.hu'),
(7, 'Molnár', 'Eszter', 'Debrecen', '2007-09-30', 'eszter@email.hu'),
(8, 'Farkas', 'Noémi', 'Miskolc', '2006-12-01', NULL);

INSERT INTO sportag VALUES
(1, 'Kosárlabda'),
(2, 'Kézilabda'),
(3, 'Futball'),
(4, 'Röplabda'),
(5, 'Úszás'),
(6, 'Tenisz');

INSERT INTO esemeny VALUES
(1, 'Tavaszi kupa', 1, 'Budapest Aréna', '2026-03-12', 20, 5000),
(2, 'Városi bajnokság', 2, 'Pécsi Sportcsarnok', '2026-04-03', 16, 4500),
(3, 'Nyári fociest', 3, 'Győri pálya', '2026-06-20', 22, 3000),
(4, 'Röplabda nap', 4, 'Budapest Aréna', '2026-05-15', 18, 3500),
(5, 'Őszi úszónap', 5, 'Debreceni Uszoda', '2026-09-10', 30, 6000),
(6, 'Tenisz délután', 6, 'Miskolci Sportpark', '2026-10-05', 12, 4000);

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

## Táblák tartalma

### sportolo

| id | vezeteknev | keresztnev | varos | szuletesi_datum | email |
|---:|---|---|---|---|---|
| 1 | Kovács | Anna | Budapest | 2006-05-12 | anna@email.hu |
| 2 | Nagy | Béla | Pécs | 2005-03-21 | bela@email.hu |
| 3 | Tóth | Kata | Budapest | 2007-08-10 | kata@email.hu |
| 4 | Szabó | Márk | Győr | 2004-11-02 | NULL |
| 5 | Varga | Lili | Pécs | 2006-07-17 | lili@email.hu |
| 6 | Kiss | Dávid | Szeged | 2005-01-25 | david@email.hu |
| 7 | Molnár | Eszter | Debrecen | 2007-09-30 | eszter@email.hu |
| 8 | Farkas | Noémi | Miskolc | 2006-12-01 | NULL |

### sportag

| id | nev |
|---:|---|
| 1 | Kosárlabda |
| 2 | Kézilabda |
| 3 | Futball |
| 4 | Röplabda |
| 5 | Úszás |
| 6 | Tenisz |

### esemeny

| id | nev | sportag_id | helyszin | datum | max_letszam | nevezesi_dij |
|---:|---|---:|---|---|---:|---:|
| 1 | Tavaszi kupa | 1 | Budapest Aréna | 2026-03-12 | 20 | 5000 |
| 2 | Városi bajnokság | 2 | Pécsi Sportcsarnok | 2026-04-03 | 16 | 4500 |
| 3 | Nyári fociest | 3 | Győri pálya | 2026-06-20 | 22 | 3000 |
| 4 | Röplabda nap | 4 | Budapest Aréna | 2026-05-15 | 18 | 3500 |
| 5 | Őszi úszónap | 5 | Debreceni Uszoda | 2026-09-10 | 30 | 6000 |
| 6 | Tenisz délután | 6 | Miskolci Sportpark | 2026-10-05 | 12 | 4000 |

### jelentkezes

| id | sportolo_id | esemeny_id | pontszam | fizetett | megjegyzes |
|---:|---:|---:|---:|---|---|
| 1 | 1 | 1 | 18 | igen | stabil teljesítmény |
| 2 | 2 | 1 | 12 | nem | késői befizetés |
| 3 | 3 | 2 | 20 | igen | kiemelkedő eredmény |
| 4 | 4 | 3 | 9 | igen | NULL |
| 5 | 5 | 2 | 15 | nem | NULL |
| 6 | 1 | 4 | 17 | igen | jó teljesítmény |
| 7 | 3 | 1 | 19 | igen | nagyon jó dobószázalék |
| 8 | 6 | 3 | 14 | igen | NULL |
| 9 | 7 | 5 | 16 | nem | első verseny |

---

# 2. Adatbázis-alapfogalmak

## Feladat

Ismertesse az adatbázis-kezelés alapfogalmait a mintaadatbázis alapján!

| Fogalom | Jelentés | Példa |
|---|---|---|
| adatbázis | kapcsolódó adatok gyűjteménye | sportverseny |
| tábla | rekordokat tároló szerkezet | sportolo |
| rekord | egy sor a táblában | Kovács Anna adatai |
| mező | egy oszlop a táblában | vezeteknev |
| elsődleges kulcs | egyedi azonosító | sportolo.id |
| idegen kulcs | másik táblára hivatkozó mező | jelentkezes.sportolo_id |

---

# 3. SQL syntax

## Példa

### Feladat

Jelenítse meg a sportolók vezetéknevét és keresztnevét!

```sql
SELECT vezeteknev,
       keresztnev
FROM sportolo;
```

### Végeredmény

| vezeteknev | keresztnev |
|---|---|
| Kovács | Anna |
| Nagy | Béla |
| Tóth | Kata |
| Szabó | Márk |
| Varga | Lili |
| Kiss | Dávid |
| Molnár | Eszter |
| Farkas | Noémi |

## Bonyolultabb példa

### Feladat

Jelenítse meg a budapesti sportolók vezetéknevét és keresztnevét vezetéknév, azon belül keresztnév szerinti növekvő sorrendben!

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

# 4. CREATE DATABASE és DROP DATABASE

## Példa

### Feladat

Hozzon létre egy `proba` nevű adatbázist!

```sql
CREATE DATABASE proba;
```

### Végeredmény

| művelet |
|---|
| Létrejön a `proba` adatbázis. |

## Bonyolultabb példa

### Feladat

Törölje a korábbi `sportverseny` adatbázist, majd hozza létre újra magyar ékezetes karakterek kezelésére alkalmas beállításokkal!

```sql
DROP DATABASE IF EXISTS sportverseny;

CREATE DATABASE sportverseny
CHARACTER SET utf8mb4
COLLATE utf8mb4_hungarian_ci;
```

### Végeredmény

| művelet |
|---|
| A korábbi adatbázis törlődik, majd létrejön az új `sportverseny` adatbázis. |

---

# 5. CREATE TABLE és DROP TABLE

## Példa

### Feladat

Hozzon létre egy egyszerű `proba_sportolo` táblát, amelyben az azonosító és a név szerepel!

```sql
CREATE TABLE proba_sportolo (
    id INT PRIMARY KEY,
    nev VARCHAR(100)
);
```

### Végeredmény

| mező | típus |
|---|---|
| id | INT PRIMARY KEY |
| nev | VARCHAR(100) |

## Bonyolultabb példa

### Feladat

Hozzon létre egy `proba_jelentkezes` táblát, amely sportolóhoz és eseményhez kapcsolódik idegen kulcsokkal!

```sql
CREATE TABLE proba_jelentkezes (
    id INT PRIMARY KEY,
    sportolo_id INT,
    esemeny_id INT,
    pontszam INT,
    FOREIGN KEY (sportolo_id) REFERENCES sportolo(id),
    FOREIGN KEY (esemeny_id) REFERENCES esemeny(id)
);
```

### Végeredmény

| mező | szerep |
|---|---|
| id | elsődleges kulcs |
| sportolo_id | idegen kulcs a sportolo táblára |
| esemeny_id | idegen kulcs az esemeny táblára |
| pontszam | egész szám |

---

# 6. Adattípusok

| Típus | Mire jó? | Példa |
|---|---|---|
| INT | egész szám | 18 |
| VARCHAR | szöveg | Kovács |
| DATE | dátum | 2026-03-12 |
| DOUBLE | valós szám | 15.56 |
| BOOLEAN | igaz/hamis érték | TRUE |

## Példa

### Feladat

Hozzon létre egy táblát, amely eseményazonosítót, eseménynevet és eseménydátumot tárol!

```sql
CREATE TABLE proba_esemeny (
    id INT,
    nev VARCHAR(100),
    datum DATE
);
```

### Végeredmény

| mező | típus |
|---|---|
| id | INT |
| nev | VARCHAR(100) |
| datum | DATE |

## Bonyolultabb példa

### Feladat

Hozzon létre egy táblát, amely sportolói eredményeket tárol: azonosító, sportoló neve, eredményátlag, aktív állapot!

```sql
CREATE TABLE sportolo_eredmeny (
    id INT PRIMARY KEY,
    nev VARCHAR(100),
    atlag DOUBLE,
    aktiv BOOLEAN
);
```

### Végeredmény

| mező | típus |
|---|---|
| id | INT PRIMARY KEY |
| nev | VARCHAR(100) |
| atlag | DOUBLE |
| aktiv | BOOLEAN |

---

# 7. PRIMARY KEY és FOREIGN KEY

## Példa

### Feladat

Mutassa be egy elsődleges kulcs megadását egy sportolókat tároló táblában!

```sql
CREATE TABLE pelda_sportolo (
    id INT PRIMARY KEY,
    nev VARCHAR(100)
);
```

### Végeredmény

| mező | jelentés |
|---|---|
| id | egyedi azonosító |
| nev | sportoló neve |

## Bonyolultabb példa

### Feladat

Mutassa be, hogyan kapcsolható össze egy jelentkezés egy sportolóval és egy eseménnyel!

```sql
CREATE TABLE pelda_jelentkezes (
    id INT PRIMARY KEY,
    sportolo_id INT,
    esemeny_id INT,
    FOREIGN KEY (sportolo_id) REFERENCES sportolo(id),
    FOREIGN KEY (esemeny_id) REFERENCES esemeny(id)
);
```

### Végeredmény

| kapcsolat | jelentés |
|---|---|
| sportolo_id → sportolo.id | a jelentkezéshez tartozó sportoló |
| esemeny_id → esemeny.id | a jelentkezéshez tartozó esemény |

---

# 8. INSERT INTO

## Példa

### Feladat

Szúrjon be egy új sportágat `Sakk` néven a `sportag` táblába!

```sql
INSERT INTO sportag
VALUES (7, 'Sakk');
```

### Végeredmény

| id | nev |
|---:|---|
| 7 | Sakk |

## Bonyolultabb példa

### Feladat

Szúrjon be egy új jelentkezést: a 2-es azonosítójú sportoló jelentkezzen a 4-es eseményre, 18 ponttal, fizetett állapottal!

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

### Végeredmény

| id | sportolo_id | esemeny_id | pontszam | fizetett | megjegyzes |
|---:|---:|---:|---:|---|---|
| 10 | 2 | 4 | 18 | igen | utolsó pillanatos nevezés |

---

# 9. SELECT

## Példa

### Feladat

Jelenítse meg a sportolók összes adatát!

```sql
SELECT *
FROM sportolo;
```

### Végeredmény

| id | vezeteknev | keresztnev | varos | szuletesi_datum | email |
|---:|---|---|---|---|---|
| 1 | Kovács | Anna | Budapest | 2006-05-12 | anna@email.hu |
| 2 | Nagy | Béla | Pécs | 2005-03-21 | bela@email.hu |
| 3 | Tóth | Kata | Budapest | 2007-08-10 | kata@email.hu |
| 4 | Szabó | Márk | Győr | 2004-11-02 | NULL |
| 5 | Varga | Lili | Pécs | 2006-07-17 | lili@email.hu |
| 6 | Kiss | Dávid | Szeged | 2005-01-25 | david@email.hu |
| 7 | Molnár | Eszter | Debrecen | 2007-09-30 | eszter@email.hu |
| 8 | Farkas | Noémi | Miskolc | 2006-12-01 | NULL |

## Bonyolultabb példa

### Feladat

Jelenítse meg a sportolók vezetéknevét, keresztnevét és városát! Csak a feladatban kért mezők szerepeljenek az eredményben.

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
| Tóth | Kata | Budapest |
| Szabó | Márk | Győr |
| Varga | Lili | Pécs |
| Kiss | Dávid | Szeged |
| Molnár | Eszter | Debrecen |
| Farkas | Noémi | Miskolc |

---

# 10. DISTINCT

## Példa

### Feladat

Jelenítse meg, mely városokból érkeztek sportolók! Egy város csak egyszer jelenjen meg.

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
| Miskolc |

## Bonyolultabb példa

### Feladat

Határozza meg, hány különböző városból érkeztek sportolók!

```sql
SELECT COUNT(DISTINCT varos) AS varosok_szama
FROM sportolo;
```

### Végeredmény

| varosok_szama |
|---:|
| 6 |

---

# 11. WHERE

## Példa

### Feladat

Listázza ki a budapesti sportolók nevét és városát!

```sql
SELECT vezeteknev,
       keresztnev,
       varos
FROM sportolo
WHERE varos = 'Budapest';
```

### Végeredmény

| vezeteknev | keresztnev | varos |
|---|---|---|
| Kovács | Anna | Budapest |
| Tóth | Kata | Budapest |

## Bonyolultabb példa

### Feladat

Listázza ki azoknak a sportolóknak a nevét és születési dátumát, akik 2006 után születtek!

```sql
SELECT vezeteknev,
       keresztnev,
       szuletesi_datum
FROM sportolo
WHERE YEAR(szuletesi_datum) > 2006;
```

### Végeredmény

| vezeteknev | keresztnev | szuletesi_datum |
|---|---|---|
| Tóth | Kata | 2007-08-10 |
| Molnár | Eszter | 2007-09-30 |

---

# 12. ORDER BY

## Példa

### Feladat

Jelenítse meg a sportolók nevét vezetéknév szerinti növekvő sorrendben!

```sql
SELECT vezeteknev,
       keresztnev
FROM sportolo
ORDER BY vezeteknev;
```

### Végeredmény

| vezeteknev | keresztnev |
|---|---|
| Farkas | Noémi |
| Kiss | Dávid |
| Kovács | Anna |
| Molnár | Eszter |
| Nagy | Béla |
| Szabó | Márk |
| Tóth | Kata |
| Varga | Lili |

## Bonyolultabb példa

### Feladat

Listázza ki a három legmagasabb pontszámú jelentkezést! Az eredményben szerepeljen a sportoló azonosítója és a pontszám, pontszám szerint csökkenő sorrendben.

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

### Feladat

Jelenítse meg azokat a sportolókat, akik Budapestről érkeztek és 2006-ban születtek!

```sql
SELECT vezeteknev,
       keresztnev
FROM sportolo
WHERE varos = 'Budapest'
AND YEAR(szuletesi_datum) = 2006;
```

### Végeredmény

| vezeteknev | keresztnev |
|---|---|
| Kovács | Anna |

## Bonyolultabb példa

### Feladat

Listázza ki azokat a sportolókat, akik Budapestről vagy Pécsről érkeztek, és 2005 után születtek!

```sql
SELECT vezeteknev,
       keresztnev,
       varos,
       szuletesi_datum
FROM sportolo
WHERE (
    varos = 'Budapest'
    OR varos = 'Pécs'
)
AND YEAR(szuletesi_datum) > 2005;
```

### Végeredmény

| vezeteknev | keresztnev | varos | szuletesi_datum |
|---|---|---|---|
| Kovács | Anna | Budapest | 2006-05-12 |
| Tóth | Kata | Budapest | 2007-08-10 |
| Varga | Lili | Pécs | 2006-07-17 |

---

# 14. UPDATE

## Példa

### Feladat

Módosítsa a 8-as azonosítójú sportoló városát `Miskolc` értékről `Eger` értékre!

```sql
UPDATE sportolo
SET varos = 'Eger'
WHERE id = 8;
```

### Végeredmény

| id | vezeteknev | keresztnev | varos |
|---:|---|---|---|
| 8 | Farkas | Noémi | Eger |

## Bonyolultabb példa

### Feladat

Állítsa át az összes nem fizetett jelentkezés fizetési állapotát `függőben` értékre!

```sql
UPDATE jelentkezes
SET fizetett = 'függőben'
WHERE fizetett = 'nem';
```

### Végeredmény

| id | fizetett |
|---:|---|
| 2 | függőben |
| 5 | függőben |
| 9 | függőben |

---

# 15. DELETE

## Példa

### Feladat

Törölje a `Tenisz` nevű sportágat a próbaadatok közül, ha korábban beszúrta!

```sql
DELETE FROM sportag
WHERE nev = 'Tenisz';
```

### Végeredmény

| művelet |
|---|
| A `Tenisz` nevű rekord törlődik a `sportag` táblából, ha nincs rá hivatkozás. |

## Bonyolultabb példa

### Feladat

Törölje azokat a jelentkezéseket, amelyekben a pontszám 10 alatti!

```sql
DELETE FROM jelentkezes
WHERE pontszam < 10;
```

### Végeredmény

| törölt jelentkezés |
|---|
| Szabó Márk 9 pontos jelentkezése törlődik. |

---

# 16. NULL értékek

## Példa

### Feladat

Listázza ki azokat a sportolókat, akiknél nincs megadva email cím!

```sql
SELECT vezeteknev,
       keresztnev,
       email
FROM sportolo
WHERE email IS NULL;
```

### Végeredmény

| vezeteknev | keresztnev | email |
|---|---|---|
| Szabó | Márk | NULL |
| Farkas | Noémi | NULL |

## Bonyolultabb példa

### Feladat

Listázza ki azoknak a sportolóknak a nevét, akiknél nincs email cím megadva, de már szerepelnek legalább egy jelentkezésben!

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

# 17. MIN és MAX

## Példa

### Feladat

Határozza meg a legmagasabb elért pontszámot!

```sql
SELECT MAX(pontszam) AS legmagasabb_pontszam
FROM jelentkezes;
```

### Végeredmény

| legmagasabb_pontszam |
|---:|
| 20 |

## Bonyolultabb példa

### Feladat

Határozza meg eseményenként a legmagasabb elért pontszámot!

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
| 3 | 14 |
| 4 | 17 |
| 5 | 16 |

---

# 18. COUNT, SUM és AVG

## Példa

### Feladat

Határozza meg a jelentkezések számát, az összes elért pontot és az átlagpontszámot!

```sql
SELECT COUNT(*) AS jelentkezesek_szama,
       SUM(pontszam) AS osszpontszam,
       ROUND(AVG(pontszam), 2) AS atlagpont
FROM jelentkezes;
```

### Végeredmény

| jelentkezesek_szama | osszpontszam | atlagpont |
|---:|---:|---:|
| 9 | 140 | 15.56 |

## Bonyolultabb példa

### Feladat

Határozza meg sportáganként az átlagpontszámot! Az eredményben a sportág neve és a két tizedesjegyre kerekített átlagpontszám szerepeljen.

```sql
SELECT sp.nev AS sportag,
       ROUND(AVG(j.pontszam), 2) AS atlagpont
FROM sportag sp
JOIN esemeny e
ON sp.id = e.sportag_id
JOIN jelentkezes j
ON e.id = j.esemeny_id
GROUP BY sp.id,
         sp.nev;
```

### Végeredmény

| sportag | atlagpont |
|---|---:|
| Kosárlabda | 16.33 |
| Kézilabda | 17.50 |
| Futball | 11.50 |
| Röplabda | 17.00 |
| Úszás | 16.00 |

---

# 19. LIKE és wildcardok

| Jel | Jelentés |
|---|---|
| `%` | tetszőleges hosszúságú karakterlánc |
| `_` | pontosan egy karakter |

## Példa

### Feladat

Listázza ki azokat a sportolókat, akiknek a vezetékneve K betűvel kezdődik!

```sql
SELECT vezeteknev,
       keresztnev
FROM sportolo
WHERE vezeteknev LIKE 'K%';
```

### Végeredmény

| vezeteknev | keresztnev |
|---|---|
| Kovács | Anna |
| Kiss | Dávid |

## Bonyolultabb példa

### Feladat

Listázza ki azokat a sportolókat, akiknek a keresztneve pontosan négy karakter hosszú és A betűvel kezdődik!

```sql
SELECT vezeteknev,
       keresztnev
FROM sportolo
WHERE keresztnev LIKE 'A___';
```

### Végeredmény

| vezeteknev | keresztnev |
|---|---|
| Kovács | Anna |

---

# 20. IN

## Példa

### Feladat

Listázza ki a budapesti és pécsi sportolókat!

```sql
SELECT vezeteknev,
       keresztnev,
       varos
FROM sportolo
WHERE varos IN ('Budapest', 'Pécs');
```

### Végeredmény

| vezeteknev | keresztnev | varos |
|---|---|---|
| Kovács | Anna | Budapest |
| Nagy | Béla | Pécs |
| Tóth | Kata | Budapest |
| Varga | Lili | Pécs |

## Bonyolultabb példa

### Feladat

Listázza ki azokat az eseményeket, amelyeket a Budapest Arénában vagy a Győri pályán rendeznek!

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
| Röplabda nap | Budapest Aréna |

---

# 21. BETWEEN

## Példa

### Feladat

Listázza ki azokat a sportolókat, akik 2005 és 2006 között születtek!

```sql
SELECT vezeteknev,
       keresztnev,
       szuletesi_datum
FROM sportolo
WHERE YEAR(szuletesi_datum)
BETWEEN 2005 AND 2006;
```

### Végeredmény

| vezeteknev | keresztnev | szuletesi_datum |
|---|---|---|
| Kovács | Anna | 2006-05-12 |
| Nagy | Béla | 2005-03-21 |
| Varga | Lili | 2006-07-17 |
| Kiss | Dávid | 2005-01-25 |
| Farkas | Noémi | 2006-12-01 |

## Bonyolultabb példa

### Feladat

Listázza ki azokat az eseményeket, amelyeket 2026. április 1. és 2026. június 30. között rendeznek!

```sql
SELECT nev,
       datum
FROM esemeny
WHERE datum BETWEEN '2026-04-01'
AND '2026-06-30'
ORDER BY datum;
```

### Végeredmény

| nev | datum |
|---|---|
| Városi bajnokság | 2026-04-03 |
| Röplabda nap | 2026-05-15 |
| Nyári fociest | 2026-06-20 |

---

# 22. Aliasok

## Példa

### Feladat

Jelenítse meg a vezetékneveket úgy, hogy az oszlop neve `vezetek` legyen!

```sql
SELECT vezeteknev AS vezetek
FROM sportolo;
```

### Végeredmény

| vezetek |
|---|
| Kovács |
| Nagy |
| Tóth |
| Szabó |
| Varga |
| Kiss |
| Molnár |
| Farkas |

## Bonyolultabb példa

### Feladat

Jelenítse meg a sportoló teljes nevét egy `teljes_nev` nevű oszlopban!

```sql
SELECT CONCAT(vezeteknev, ' ', keresztnev) AS teljes_nev
FROM sportolo;
```

### Végeredmény

| teljes_nev |
|---|
| Kovács Anna |
| Nagy Béla |
| Tóth Kata |
| Szabó Márk |
| Varga Lili |
| Kiss Dávid |
| Molnár Eszter |
| Farkas Noémi |

---

# 23. CONCAT és számított mezők

## Példa

### Feladat

Jelenítse meg a sportolók teljes nevét!

```sql
SELECT CONCAT(vezeteknev, ' ', keresztnev) AS teljes_nev
FROM sportolo;
```

### Végeredmény

| teljes_nev |
|---|
| Kovács Anna |
| Nagy Béla |
| Tóth Kata |
| Szabó Márk |
| Varga Lili |
| Kiss Dávid |
| Molnár Eszter |
| Farkas Noémi |

## Bonyolultabb példa

### Feladat

Jelenítse meg a sportolók nevét és városát egyetlen szöveges mezőben, az alábbi formában: `Kovács Anna - Budapest`.

```sql
SELECT CONCAT(
    vezeteknev,
    ' ',
    keresztnev,
    ' - ',
    varos
) AS sportolo_adat
FROM sportolo;
```

### Végeredmény

| sportolo_adat |
|---|
| Kovács Anna - Budapest |
| Nagy Béla - Pécs |
| Tóth Kata - Budapest |
| Szabó Márk - Győr |
| Varga Lili - Pécs |
| Kiss Dávid - Szeged |
| Molnár Eszter - Debrecen |
| Farkas Noémi - Miskolc |

---

# 24. Dátumkezelés

| Függvény | Jelentés |
|---|---|
| YEAR() | év |
| MONTH() | hónap |
| DAY() | nap |
| DATEDIFF() | két dátum különbsége napokban |
| DATE_FORMAT() | dátum formázása |

## Példa

### Feladat

Listázza ki a májusban megrendezett eseményeket!

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

## Bonyolultabb példa

### Feladat

Jelenítse meg az események nevét, dátumát, valamint azt, hogy hány nap telik el 2026. március 1. és az esemény dátuma között! Az eredmény dátum szerint növekvő sorrendben jelenjen meg.

```sql
SELECT nev,
       datum,
       DATEDIFF(datum, '2026-03-01') AS napok_szama
FROM esemeny
ORDER BY datum;
```

### Végeredmény

| nev | datum | napok_szama |
|---|---|---:|
| Tavaszi kupa | 2026-03-12 | 11 |
| Városi bajnokság | 2026-04-03 | 33 |
| Röplabda nap | 2026-05-15 | 75 |
| Nyári fociest | 2026-06-20 | 111 |
| Őszi úszónap | 2026-09-10 | 193 |
| Tenisz délután | 2026-10-05 | 218 |

## Bonyolultabb dátumformázás

### Feladat

Jelenítse meg az események dátumát pontozott formában: `2026.03.12`.

```sql
SELECT nev,
       DATE_FORMAT(datum, '%Y.%m.%d') AS formatalt_datum
FROM esemeny;
```

### Végeredmény

| nev | formatalt_datum |
|---|---|
| Tavaszi kupa | 2026.03.12 |
| Városi bajnokság | 2026.04.03 |
| Nyári fociest | 2026.06.20 |
| Röplabda nap | 2026.05.15 |
| Őszi úszónap | 2026.09.10 |
| Tenisz délután | 2026.10.05 |

---

# 25. CASE

## Példa

### Feladat

Minősítse a jelentkezéseket pontszám alapján! A legalább 18 pontos eredmény legyen `kiváló`, minden más legyen `egyéb`.

```sql
SELECT pontszam,
       CASE
           WHEN pontszam >= 18 THEN 'kiváló'
           ELSE 'egyéb'
       END AS minosites
FROM jelentkezes;
```

### Végeredmény

| pontszam | minosites |
|---:|---|
| 18 | kiváló |
| 12 | egyéb |
| 20 | kiváló |
| 9 | egyéb |
| 15 | egyéb |
| 17 | egyéb |
| 19 | kiváló |
| 14 | egyéb |
| 16 | egyéb |

## Bonyolultabb példa

### Feladat

Jelenítse meg a sportolók teljes nevét, pontszámát és teljesítményük minősítését! 18 ponttól `kiváló`, 14 ponttól `jó`, egyébként `fejlesztendő` legyen a minősítés.

```sql
SELECT CONCAT(s.vezeteknev, ' ', s.keresztnev) AS sportolo,
       j.pontszam,
       CASE
           WHEN j.pontszam >= 18 THEN 'kiváló'
           WHEN j.pontszam >= 14 THEN 'jó'
           ELSE 'fejlesztendő'
       END AS minosites
FROM sportolo s
JOIN jelentkezes j
ON s.id = j.sportolo_id
ORDER BY j.pontszam DESC;
```

### Végeredmény

| sportolo | pontszam | minosites |
|---|---:|---|
| Tóth Kata | 20 | kiváló |
| Tóth Kata | 19 | kiváló |
| Kovács Anna | 18 | kiváló |
| Kovács Anna | 17 | jó |
| Molnár Eszter | 16 | jó |
| Varga Lili | 15 | jó |
| Kiss Dávid | 14 | jó |
| Nagy Béla | 12 | fejlesztendő |
| Szabó Márk | 9 | fejlesztendő |

---

# 26. JOIN

## Példa

### Feladat

Jelenítse meg a sportolók vezetéknevét és az általuk elért pontszámokat!

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
| Tóth | 20 |
| Szabó | 9 |
| Varga | 15 |
| Kovács | 17 |
| Tóth | 19 |
| Kiss | 14 |
| Molnár | 16 |

## Bonyolultabb példa

### Feladat

Jelenítse meg a sportoló teljes nevét, az esemény nevét és az elért pontszámot! Az eredmény pontszám szerint csökkenő sorrendben jelenjen meg.

```sql
SELECT CONCAT(s.vezeteknev, ' ', s.keresztnev) AS sportolo,
       e.nev AS esemeny,
       j.pontszam
FROM sportolo s
JOIN jelentkezes j
ON s.id = j.sportolo_id
JOIN esemeny e
ON e.id = j.esemeny_id
ORDER BY j.pontszam DESC;
```

### Végeredmény

| sportolo | esemeny | pontszam |
|---|---|---:|
| Tóth Kata | Városi bajnokság | 20 |
| Tóth Kata | Tavaszi kupa | 19 |
| Kovács Anna | Tavaszi kupa | 18 |
| Kovács Anna | Röplabda nap | 17 |
| Molnár Eszter | Őszi úszónap | 16 |
| Varga Lili | Városi bajnokság | 15 |
| Kiss Dávid | Nyári fociest | 14 |
| Nagy Béla | Tavaszi kupa | 12 |
| Szabó Márk | Nyári fociest | 9 |

---

# 27. LEFT JOIN

## Példa

### Feladat

Jelenítse meg az összes sportolót, és ha van jelentkezésük, annak azonosítóját is!

```sql
SELECT s.vezeteknev,
       s.keresztnev,
       j.id AS jelentkezes_id
FROM sportolo s
LEFT JOIN jelentkezes j
ON s.id = j.sportolo_id;
```

### Végeredmény

| vezeteknev | keresztnev | jelentkezes_id |
|---|---|---:|
| Kovács | Anna | 1 |
| Nagy | Béla | 2 |
| Tóth | Kata | 3 |
| Szabó | Márk | 4 |
| Varga | Lili | 5 |
| Kovács | Anna | 6 |
| Tóth | Kata | 7 |
| Kiss | Dávid | 8 |
| Molnár | Eszter | 9 |
| Farkas | Noémi | NULL |

## Bonyolultabb példa

### Feladat

Listázza ki azokat a sportolókat, akik nem jelentkeztek egyetlen eseményre sem!

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
| Farkas | Noémi |

---

# 28. GROUP BY

## Példa

### Feladat

Határozza meg, hány sportoló érkezett az egyes városokból!

```sql
SELECT varos,
       COUNT(*) AS sportolok_szama
FROM sportolo
GROUP BY varos;
```

### Végeredmény

| varos | sportolok_szama |
|---|---:|
| Budapest | 2 |
| Debrecen | 1 |
| Győr | 1 |
| Miskolc | 1 |
| Pécs | 2 |
| Szeged | 1 |

## Bonyolultabb példa

### Feladat

Határozza meg sportáganként a jelentkezések számát! Az eredményben a sportág neve és a jelentkezések száma szerepeljen, a legtöbb jelentkezéstől haladva.

```sql
SELECT sp.nev AS sportag,
       COUNT(j.id) AS jelentkezesek
FROM sportag sp
JOIN esemeny e
ON sp.id = e.sportag_id
JOIN jelentkezes j
ON e.id = j.esemeny_id
GROUP BY sp.id,
         sp.nev
ORDER BY jelentkezesek DESC;
```

### Végeredmény

| sportag | jelentkezesek |
|---|---:|
| Kosárlabda | 3 |
| Futball | 2 |
| Kézilabda | 2 |
| Röplabda | 1 |
| Úszás | 1 |

---

# 29. HAVING

## Példa

### Feladat

Listázza ki azokat a városokat, ahonnan legalább két sportoló érkezett!

```sql
SELECT varos,
       COUNT(*) AS sportolok_szama
FROM sportolo
GROUP BY varos
HAVING COUNT(*) >= 2;
```

### Végeredmény

| varos | sportolok_szama |
|---|---:|
| Budapest | 2 |
| Pécs | 2 |

## Bonyolultabb példa

### Feladat

Listázza ki azokat az eseményeket, amelyekre legalább két jelentkezés érkezett!

```sql
SELECT e.nev AS esemeny,
       COUNT(j.id) AS jelentkezok_szama
FROM esemeny e
JOIN jelentkezes j
ON e.id = j.esemeny_id
GROUP BY e.id,
         e.nev
HAVING COUNT(j.id) >= 2;
```

### Végeredmény

| esemeny | jelentkezok_szama |
|---|---:|
| Tavaszi kupa | 3 |
| Városi bajnokság | 2 |
| Nyári fociest | 2 |

---

# 30. Függvénykombinációk

## Példa

### Feladat

Határozza meg a jelentkezések átlagpontszámát két tizedesjegyre kerekítve!

```sql
SELECT ROUND(AVG(pontszam), 2) AS atlagpont
FROM jelentkezes;
```

### Végeredmény

| atlagpont |
|---:|
| 15.56 |

## Bonyolultabb példa

### Feladat

Jelenítse meg sportolónként a jelentkezések számát és az átlagpontszámot! Az átlagpontszám két tizedesjegyre kerekítve jelenjen meg.

```sql
SELECT CONCAT(s.vezeteknev, ' ', s.keresztnev) AS sportolo,
       COUNT(j.id) AS jelentkezesek_szama,
       ROUND(AVG(j.pontszam), 2) AS atlagpont
FROM sportolo s
JOIN jelentkezes j
ON s.id = j.sportolo_id
GROUP BY s.id,
         s.vezeteknev,
         s.keresztnev
ORDER BY atlagpont DESC;
```

### Végeredmény

| sportolo | jelentkezesek_szama | atlagpont |
|---|---:|---:|
| Tóth Kata | 2 | 19.50 |
| Kovács Anna | 2 | 17.50 |
| Molnár Eszter | 1 | 16.00 |
| Varga Lili | 1 | 15.00 |
| Kiss Dávid | 1 | 14.00 |
| Nagy Béla | 1 | 12.00 |
| Szabó Márk | 1 | 9.00 |

---

# 31. Allekérdezések

## Példa

### Feladat

Jelenítse meg azt a jelentkezést, amelyben a legmagasabb pontszám született!

```sql
SELECT *
FROM jelentkezes
WHERE pontszam = (
    SELECT MAX(pontszam)
    FROM jelentkezes
);
```

### Végeredmény

| id | sportolo_id | esemeny_id | pontszam | fizetett | megjegyzes |
|---:|---:|---:|---:|---|---|
| 3 | 3 | 2 | 20 | igen | kiemelkedő eredmény |

## Bonyolultabb példa

### Feladat

Listázza ki azokat a sportolókat, akik az összes jelentkezés átlagpontszámánál jobb eredményt értek el!

```sql
SELECT CONCAT(s.vezeteknev, ' ', s.keresztnev) AS sportolo,
       j.pontszam
FROM sportolo s
JOIN jelentkezes j
ON s.id = j.sportolo_id
WHERE j.pontszam > (
    SELECT AVG(pontszam)
    FROM jelentkezes
)
ORDER BY j.pontszam DESC;
```

### Végeredmény

| sportolo | pontszam |
|---|---:|
| Tóth Kata | 20 |
| Tóth Kata | 19 |
| Kovács Anna | 18 |
| Kovács Anna | 17 |
| Molnár Eszter | 16 |

## Összetettebb példa

### Feladat

Listázza ki eseményenként azt a sportolót, aki az adott eseményen a legjobb pontszámot érte el!

```sql
SELECT e.nev AS esemeny,
       CONCAT(s.vezeteknev, ' ', s.keresztnev) AS sportolo,
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
)
ORDER BY e.datum;
```

### Végeredmény

| esemeny | sportolo | pontszam |
|---|---|---:|
| Tavaszi kupa | Tóth Kata | 19 |
| Városi bajnokság | Tóth Kata | 20 |
| Röplabda nap | Kovács Anna | 17 |
| Nyári fociest | Kiss Dávid | 14 |
| Őszi úszónap | Molnár Eszter | 16 |

---

# 32. EXISTS

## Példa

### Feladat

Listázza ki azokat a sportolókat, akikhez tartozik legalább egy jelentkezés!

```sql
SELECT s.vezeteknev,
       s.keresztnev
FROM sportolo s
WHERE EXISTS (
    SELECT 1
    FROM jelentkezes j
    WHERE j.sportolo_id = s.id
);
```

### Végeredmény

| vezeteknev | keresztnev |
|---|---|
| Kovács | Anna |
| Nagy | Béla |
| Tóth | Kata |
| Szabó | Márk |
| Varga | Lili |
| Kiss | Dávid |
| Molnár | Eszter |

## Bonyolultabb példa

### Feladat

Listázza ki azokat az eseményeket, amelyekre érkezett legalább egy jelentkezés!

```sql
SELECT e.nev,
       e.datum
FROM esemeny e
WHERE EXISTS (
    SELECT 1
    FROM jelentkezes j
    WHERE j.esemeny_id = e.id
);
```

### Végeredmény

| nev | datum |
|---|---|
| Tavaszi kupa | 2026-03-12 |
| Városi bajnokság | 2026-04-03 |
| Nyári fociest | 2026-06-20 |
| Röplabda nap | 2026-05-15 |
| Őszi úszónap | 2026-09-10 |

---

# 33. CREATE TABLE AS

## Példa

### Feladat

Hozzon létre egy `budapestiek` nevű táblát a budapesti sportolókból!

```sql
CREATE TABLE budapestiek AS
SELECT *
FROM sportolo
WHERE varos = 'Budapest';
```

### Végeredmény

| id | vezeteknev | keresztnev | varos |
|---:|---|---|---|
| 1 | Kovács | Anna | Budapest |
| 3 | Tóth | Kata | Budapest |

## Bonyolultabb példa

### Feladat

Hozzon létre egy `legjobbak` nevű táblát azokból a jelentkezésekből, ahol a pontszám legalább 18!

```sql
CREATE TABLE legjobbak AS
SELECT sportolo_id,
       esemeny_id,
       pontszam
FROM jelentkezes
WHERE pontszam >= 18;
```

### Végeredmény

| sportolo_id | esemeny_id | pontszam |
|---:|---:|---:|
| 1 | 1 | 18 |
| 3 | 2 | 20 |
| 3 | 1 | 19 |

---

# 34. INSERT INTO SELECT

## Példa

### Feladat

Szúrja be a `budapestiek` táblába a budapesti sportolókat!

```sql
INSERT INTO budapestiek
SELECT *
FROM sportolo
WHERE varos = 'Budapest';
```

### Végeredmény

| művelet |
|---|
| A budapesti sportolók rekordjai bekerülnek a `budapestiek` táblába. |

## Bonyolultabb példa

### Feladat

Szúrja be a `legjobbak` táblába azokat a jelentkezéseket, amelyek pontszáma magasabb az összes jelentkezés átlagánál!

```sql
INSERT INTO legjobbak
SELECT sportolo_id,
       esemeny_id,
       pontszam
FROM jelentkezes
WHERE pontszam > (
    SELECT AVG(pontszam)
    FROM jelentkezes
);
```

### Végeredmény

| sportolo_id | esemeny_id | pontszam |
|---:|---:|---:|
| 1 | 1 | 18 |
| 3 | 2 | 20 |
| 1 | 4 | 17 |
| 3 | 1 | 19 |
| 7 | 5 | 16 |

---

# 35. Összetett érettségi feladatok

## 1. feladat

### Feladat

Készítsen lekérdezést, amely eseményenként megadja a legjobb eredményt elérő sportoló nevét és pontszámát! Az eredményben az esemény neve, a sportoló teljes neve és a pontszám szerepeljen.

```sql
SELECT e.nev AS esemeny,
       CONCAT(s.vezeteknev, ' ', s.keresztnev) AS sportolo,
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
)
ORDER BY e.datum;
```

### Végeredmény

| esemeny | sportolo | pontszam |
|---|---|---:|
| Tavaszi kupa | Tóth Kata | 19 |
| Városi bajnokság | Tóth Kata | 20 |
| Röplabda nap | Kovács Anna | 17 |
| Nyári fociest | Kiss Dávid | 14 |
| Őszi úszónap | Molnár Eszter | 16 |

## 2. feladat

### Feladat

Készítsen lekérdezést, amely sportáganként megadja az átlagpontszámot, de csak azok a sportágak jelenjenek meg, ahol az átlagpontszám nagyobb 15-nél!

```sql
SELECT sp.nev AS sportag,
       ROUND(AVG(j.pontszam), 2) AS atlagpont
FROM sportag sp
JOIN esemeny e
ON sp.id = e.sportag_id
JOIN jelentkezes j
ON e.id = j.esemeny_id
GROUP BY sp.id,
         sp.nev
HAVING AVG(j.pontszam) > 15
ORDER BY atlagpont DESC;
```

### Végeredmény

| sportag | atlagpont |
|---|---:|
| Kézilabda | 17.50 |
| Röplabda | 17.00 |
| Kosárlabda | 16.33 |
| Úszás | 16.00 |

## 3. feladat

### Feladat

Készítsen lekérdezést, amely eseményenként megadja a jelentkezők számát, a maximális létszámot és a telítettséget százalékban!

```sql
SELECT e.nev AS esemeny,
       COUNT(j.id) AS jelentkezok_szama,
       e.max_letszam,
       ROUND(COUNT(j.id) / e.max_letszam * 100, 2) AS telitettseg_szazalek
FROM esemeny e
LEFT JOIN jelentkezes j
ON e.id = j.esemeny_id
GROUP BY e.id,
         e.nev,
         e.max_letszam
ORDER BY telitettseg_szazalek DESC;
```

### Végeredmény

| esemeny | jelentkezok_szama | max_letszam | telitettseg_szazalek |
|---|---:|---:|---:|
| Tavaszi kupa | 3 | 20 | 15.00 |
| Városi bajnokság | 2 | 16 | 12.50 |
| Nyári fociest | 2 | 22 | 9.09 |
| Röplabda nap | 1 | 18 | 5.56 |
| Őszi úszónap | 1 | 30 | 3.33 |
| Tenisz délután | 0 | 12 | 0.00 |

## 4. feladat

### Feladat

Készítsen lekérdezést, amely sportolónként megadja, hány eseményre jelentkezett, és mennyi az átlagpontszáma! Csak azok jelenjenek meg, akik legalább két eseményre jelentkeztek.

```sql
SELECT CONCAT(s.vezeteknev, ' ', s.keresztnev) AS sportolo,
       COUNT(j.id) AS jelentkezesek_szama,
       ROUND(AVG(j.pontszam), 2) AS atlagpont
FROM sportolo s
JOIN jelentkezes j
ON s.id = j.sportolo_id
GROUP BY s.id,
         s.vezeteknev,
         s.keresztnev
HAVING COUNT(j.id) >= 2
ORDER BY atlagpont DESC;
```

### Végeredmény

| sportolo | jelentkezesek_szama | atlagpont |
|---|---:|---:|
| Tóth Kata | 2 | 19.50 |
| Kovács Anna | 2 | 17.50 |

---

# 36. Tipikus érettségi hibák

## NULL hibás kezelése

Hibás:

```sql
WHERE email = NULL
```

Helyes:

```sql
WHERE email IS NULL
```

## COUNT használata WHERE-ben

Hibás:

```sql
WHERE COUNT(*) > 2
```

Helyes:

```sql
HAVING COUNT(*) > 2
```

## Rossz JOIN kapcsolat

Hibás:

```sql
ON sportolo.id = esemeny.id
```

Helyes gondolkodás:

```text
sportolo → jelentkezes → esemeny
```

Helyes példa:

```sql
SELECT s.vezeteknev,
       e.nev
FROM sportolo s
JOIN jelentkezes j
ON s.id = j.sportolo_id
JOIN esemeny e
ON e.id = j.esemeny_id;
```

## LIKE helyett egyenlőségjel

Hibás:

```sql
WHERE vezeteknev = 'K%'
```

Helyes:

```sql
WHERE vezeteknev LIKE 'K%'
```

## GROUP BY hiánya

Hibás:

```sql
SELECT varos,
       COUNT(*)
FROM sportolo;
```

Helyes:

```sql
SELECT varos,
       COUNT(*)
FROM sportolo
GROUP BY varos;
```
