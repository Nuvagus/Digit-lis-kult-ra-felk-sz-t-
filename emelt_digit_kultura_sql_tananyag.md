# Emelt szintű digitális kultúra érettségi – SQL és adatbázis-kezelés

## Bevezetés

Az adatbázis-kezelés az emelt szintű digitális kultúra érettségi egyik legfontosabb gyakorlati témaköre. Elsőre sokan tartanak tőle, mert az SQL kódolásnak tűnik, de valójában nagyon szabályos logikára épül.

A legtöbb feladatnál nem az a kérdés, hogy „tudsz-e programozni”, hanem az, hogy felismered-e:

- melyik táblából kell dolgozni,
- melyik mezőre van szükség,
- kell-e szűrés,
- kell-e rendezés,
- kell-e csoportosítás,
- kell-e több tábla összekapcsolása.

Ebben az anyagban végig ugyanazzal a mintaadatbázissal dolgozunk. Minden SQL-példa után szerepel a várható eredménytábla is, mert így lehet igazán megérteni, mit csinál a lekérdezés.

A tananyag MySQL/MariaDB szemléletben készült, ami jól illeszkedik az érettségin gyakran használt XAMPP/phpMyAdmin környezethez.

---

# Tartalomjegyzék

1. SQL Syntax
2. Adatbázis létrehozása
3. SELECT
4. SELECT DISTINCT
5. WHERE
6. ORDER BY
7. AND / OR / NOT
8. INSERT INTO
9. NULL értékek
10. UPDATE
11. DELETE
12. SELECT TOP / LIMIT
13. Aggregáló függvények
14. MIN
15. MAX
16. COUNT
17. SUM
18. AVG
19. LIKE
20. Wildcard karakterek
21. IN
22. BETWEEN
23. Aliasok
24. JOIN
25. INNER JOIN
26. LEFT JOIN
27. RIGHT JOIN
28. FULL OUTER JOIN
29. SELF JOIN
30. UNION
31. GROUP BY
32. HAVING
33. EXISTS
34. ANY / ALL
35. SELECT INTO / CREATE TABLE AS
36. INSERT INTO SELECT
37. CASE
38. NULL függvények
39. SQL kommentek
40. SQL operátorok
41. Allekérdezések
42. Érettségi típusfeladatok
43. Összetett lekérdezések

---

# 1. SQL Syntax

Az SQL lekérdezéseknek van egy megszokott szerkezete.

## Alap syntax

```sql
SELECT oszlop1, oszlop2
FROM tabla_neve
WHERE feltetel
ORDER BY oszlop;
```

Nem minden rész kötelező. A legegyszerűbb lekérdezésben csak `SELECT` és `FROM` szerepel.

## Példa

```sql
SELECT vezeteknev, keresztnev
FROM sportolo;
```

## Magyarázat

- `SELECT` → mit szeretnénk megjeleníteni
- `FROM` → melyik táblából kérjük az adatokat
- `WHERE` → milyen feltétel alapján szűrünk
- `ORDER BY` → milyen sorrendben jelenjen meg az eredmény

## Fontos

Az SQL-ben a kulcsszavakat szokás nagybetűvel írni:

```sql
SELECT
FROM
WHERE
ORDER BY
```

Ez nem kötelező, de olvashatóbbá teszi a megoldást.

---

# 2. Adatbázis létrehozása

Az érettségin általában készen kapod az adatbázist vagy az importálandó forrásfájlokat. Tanuláshoz viszont hasznos, ha mi magunk hozzuk létre a teljes adatbázist.

A példában egy sportverseny adatbázist készítünk.

## Teljes adatbázis-kreáló script

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
    szuletesi_ev INT,
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
(1, 'Kovács', 'Anna', 'Budapest', 2006, 'anna.kovacs@email.hu'),
(2, 'Nagy', 'Béla', 'Pécs', 2005, 'bela.nagy@email.hu'),
(3, 'Tóth', 'Kata', 'Budapest', 2007, 'kata.toth@email.hu'),
(4, 'Szabó', 'Márk', 'Győr', 2004, NULL),
(5, 'Varga', 'Lili', 'Pécs', 2006, 'lili.varga@email.hu'),
(6, 'Kiss', 'Dávid', 'Szeged', 2005, NULL),
(7, 'Molnár', 'Eszter', 'Debrecen', 2007, 'eszter.molnar@email.hu');

INSERT INTO sportag VALUES
(1, 'kosárlabda'),
(2, 'kézilabda'),
(3, 'futball'),
(4, 'röplabda'),
(5, 'úszás');

INSERT INTO esemeny VALUES
(1, 'Tavaszi kupa', 1, 'Budapest Aréna', '2026-03-12', 20, 3000),
(2, 'Városi bajnokság', 2, 'Pécsi Sportcsarnok', '2026-04-03', 16, 2500),
(3, 'Nyári fociest', 3, 'Győri pálya', '2026-06-20', 22, 2000),
(4, 'Röplabda nap', 4, 'Budapest Aréna', '2026-05-15', 18, 2800),
(5, 'Őszi úszónap', 5, 'Debreceni Uszoda', '2026-09-10', 12, 3500);

INSERT INTO jelentkezes VALUES
(1, 1, 1, 18, 'igen', 'időben fizetett'),
(2, 2, 1, 12, 'nem', NULL),
(3, 3, 2, 20, 'igen', 'kiemelt teljesítmény'),
(4, 4, 3, 9, 'igen', NULL),
(5, 5, 2, 15, 'nem', 'fizetésre vár'),
(6, 1, 4, 17, 'igen', NULL),
(7, 3, 1, 19, 'igen', 'második nevezés'),
(8, 6, 3, 14, 'igen', NULL),
(9, 7, 5, 16, 'nem', NULL);
```

---

## sportolo tábla

| id | vezeteknev | keresztnev | varos | szuletesi_ev | email |
|---:|---|---|---|---:|---|
| 1 | Kovács | Anna | Budapest | 2006 | anna.kovacs@email.hu |
| 2 | Nagy | Béla | Pécs | 2005 | bela.nagy@email.hu |
| 3 | Tóth | Kata | Budapest | 2007 | kata.toth@email.hu |
| 4 | Szabó | Márk | Győr | 2004 | NULL |
| 5 | Varga | Lili | Pécs | 2006 | lili.varga@email.hu |
| 6 | Kiss | Dávid | Szeged | 2005 | NULL |
| 7 | Molnár | Eszter | Debrecen | 2007 | eszter.molnar@email.hu |

---

## sportag tábla

| id | nev |
|---:|---|
| 1 | kosárlabda |
| 2 | kézilabda |
| 3 | futball |
| 4 | röplabda |
| 5 | úszás |

---

## esemeny tábla

| id | nev | sportag_id | helyszin | datum | max_letszam | nevezesi_dij |
|---:|---|---:|---|---|---:|---:|
| 1 | Tavaszi kupa | 1 | Budapest Aréna | 2026-03-12 | 20 | 3000 |
| 2 | Városi bajnokság | 2 | Pécsi Sportcsarnok | 2026-04-03 | 16 | 2500 |
| 3 | Nyári fociest | 3 | Győri pálya | 2026-06-20 | 22 | 2000 |
| 4 | Röplabda nap | 4 | Budapest Aréna | 2026-05-15 | 18 | 2800 |
| 5 | Őszi úszónap | 5 | Debreceni Uszoda | 2026-09-10 | 12 | 3500 |

---

## jelentkezes tábla

| id | sportolo_id | esemeny_id | pontszam | fizetett | megjegyzes |
|---:|---:|---:|---:|---|---|
| 1 | 1 | 1 | 18 | igen | időben fizetett |
| 2 | 2 | 1 | 12 | nem | NULL |
| 3 | 3 | 2 | 20 | igen | kiemelt teljesítmény |
| 4 | 4 | 3 | 9 | igen | NULL |
| 5 | 5 | 2 | 15 | nem | fizetésre vár |
| 6 | 1 | 4 | 17 | igen | NULL |
| 7 | 3 | 1 | 19 | igen | második nevezés |
| 8 | 6 | 3 | 14 | igen | NULL |
| 9 | 7 | 5 | 16 | nem | NULL |

---

# 3. SELECT

A `SELECT` segítségével adatokat kérdezünk le egy táblából.

## Syntax

```sql
SELECT oszlop1, oszlop2
FROM tabla_neve;
```

## Példa: minden sportoló teljes adata

```sql
SELECT *
FROM sportolo;
```

## Végeredmény

| id | vezeteknev | keresztnev | varos | szuletesi_ev | email |
|---:|---|---|---|---:|---|
| 1 | Kovács | Anna | Budapest | 2006 | anna.kovacs@email.hu |
| 2 | Nagy | Béla | Pécs | 2005 | bela.nagy@email.hu |
| 3 | Tóth | Kata | Budapest | 2007 | kata.toth@email.hu |
| 4 | Szabó | Márk | Győr | 2004 | NULL |
| 5 | Varga | Lili | Pécs | 2006 | lili.varga@email.hu |
| 6 | Kiss | Dávid | Szeged | 2005 | NULL |
| 7 | Molnár | Eszter | Debrecen | 2007 | eszter.molnar@email.hu |

## Példa: csak név és város

```sql
SELECT vezeteknev, keresztnev, varos
FROM sportolo;
```

## Végeredmény

| vezeteknev | keresztnev | varos |
|---|---|---|
| Kovács | Anna | Budapest |
| Nagy | Béla | Pécs |
| Tóth | Kata | Budapest |
| Szabó | Márk | Győr |
| Varga | Lili | Pécs |
| Kiss | Dávid | Szeged |
| Molnár | Eszter | Debrecen |

## Magyarázat

A `SELECT *` minden oszlopot megjelenít. Érettségin viszont gyakran pontosan megadják, mely mezők jelenjenek meg. Ilyenkor jobb csak azokat kiírni.

---

# 4. SELECT DISTINCT

A `DISTINCT` eltávolítja az ismétlődő sorokat.

## Syntax

```sql
SELECT DISTINCT oszlop
FROM tabla_neve;
```

## Példa: városok ismétlődés nélkül

```sql
SELECT DISTINCT varos
FROM sportolo;
```

## Végeredmény

| varos |
|---|
| Budapest |
| Pécs |
| Győr |
| Szeged |
| Debrecen |

## Magyarázat

A `sportolo` táblában Budapest és Pécs többször szerepel. A `DISTINCT` csak egyszer jeleníti meg őket.

## Tipikus érettségi jelzés

Ha a feladatban ilyen szavak vannak:

- különböző
- egyedi
- ismétlődés nélkül
- mely városokból

akkor gondolj a `DISTINCT` használatára.

---

# 5. WHERE

A `WHERE` sorok szűrésére szolgál.

## Syntax

```sql
SELECT oszlopok
FROM tabla_neve
WHERE feltetel;
```

## Példa: budapesti sportolók

```sql
SELECT vezeteknev, keresztnev, varos
FROM sportolo
WHERE varos = 'Budapest';
```

## Végeredmény

| vezeteknev | keresztnev | varos |
|---|---|---|
| Kovács | Anna | Budapest |
| Tóth | Kata | Budapest |

## Példa: 2006 után született sportolók

```sql
SELECT vezeteknev, keresztnev, szuletesi_ev
FROM sportolo
WHERE szuletesi_ev > 2006;
```

## Végeredmény

| vezeteknev | keresztnev | szuletesi_ev |
|---|---|---:|
| Tóth | Kata | 2007 |
| Molnár | Eszter | 2007 |

## Operátorok

| Operátor | Jelentés |
|---|---|
| = | egyenlő |
| <> | nem egyenlő |
| > | nagyobb |
| < | kisebb |
| >= | nagyobb vagy egyenlő |
| <= | kisebb vagy egyenlő |

---

# 6. ORDER BY

Az `ORDER BY` rendezi az eredménytáblát.

## Syntax

```sql
SELECT oszlopok
FROM tabla_neve
ORDER BY oszlop ASC;
```

vagy:

```sql
SELECT oszlopok
FROM tabla_neve
ORDER BY oszlop DESC;
```

## Példa: sportolók vezetéknév szerint

```sql
SELECT vezeteknev, keresztnev
FROM sportolo
ORDER BY vezeteknev ASC;
```

## Végeredmény

| vezeteknev | keresztnev |
|---|---|
| Kiss | Dávid |
| Kovács | Anna |
| Molnár | Eszter |
| Nagy | Béla |
| Szabó | Márk |
| Tóth | Kata |
| Varga | Lili |

## Példa: jelentkezések pontszám szerint csökkenően

```sql
SELECT sportolo_id, pontszam
FROM jelentkezes
ORDER BY pontszam DESC;
```

## Végeredmény

| sportolo_id | pontszam |
|---:|---:|
| 3 | 20 |
| 3 | 19 |
| 1 | 18 |
| 1 | 17 |
| 7 | 16 |
| 5 | 15 |
| 6 | 14 |
| 2 | 12 |
| 4 | 9 |

## Magyarázat

- `ASC` = növekvő sorrend
- `DESC` = csökkenő sorrend

Ha nincs megadva, az SQL alapból növekvő sorrendet használ.

---

# 7. AND / OR / NOT

Több feltételt is megadhatunk.

---

## AND

Az `AND` esetén minden feltételnek teljesülnie kell.

### Syntax

```sql
SELECT oszlopok
FROM tabla
WHERE feltetel1 AND feltetel2;
```

### Példa

```sql
SELECT vezeteknev, keresztnev, varos, szuletesi_ev
FROM sportolo
WHERE varos = 'Budapest'
AND szuletesi_ev = 2006;
```

### Végeredmény

| vezeteknev | keresztnev | varos | szuletesi_ev |
|---|---|---|---:|
| Kovács | Anna | Budapest | 2006 |

---

## OR

Az `OR` esetén elég, ha az egyik feltétel teljesül.

### Példa

```sql
SELECT vezeteknev, keresztnev, varos
FROM sportolo
WHERE varos = 'Budapest'
OR varos = 'Pécs';
```

### Végeredmény

| vezeteknev | keresztnev | varos |
|---|---|---|
| Kovács | Anna | Budapest |
| Nagy | Béla | Pécs |
| Tóth | Kata | Budapest |
| Varga | Lili | Pécs |

---

## NOT

A `NOT` tagadja a feltételt.

### Példa

```sql
SELECT vezeteknev, keresztnev, varos
FROM sportolo
WHERE NOT varos = 'Budapest';
```

### Végeredmény

| vezeteknev | keresztnev | varos |
|---|---|---|
| Nagy | Béla | Pécs |
| Szabó | Márk | Győr |
| Varga | Lili | Pécs |
| Kiss | Dávid | Szeged |
| Molnár | Eszter | Debrecen |

## Fontos

Ez ugyanazt adja, mint:

```sql
WHERE varos <> 'Budapest'
```

---

# 8. INSERT INTO

Az `INSERT INTO` új rekord beszúrására szolgál.

Érettségin ritkábban kell új rekordot beszúrni, de a működését érdemes érteni.

## Syntax

```sql
INSERT INTO tabla_neve (oszlop1, oszlop2)
VALUES (ertek1, ertek2);
```

## Példa

```sql
INSERT INTO sportolo (id, vezeteknev, keresztnev, varos, szuletesi_ev, email)
VALUES (8, 'Farkas', 'Noémi', 'Miskolc', 2006, 'noemi.farkas@email.hu');
```

## Beszúrás utáni lekérdezés

```sql
SELECT id, vezeteknev, keresztnev, varos
FROM sportolo
WHERE id = 8;
```

## Végeredmény

| id | vezeteknev | keresztnev | varos |
|---:|---|---|---|
| 8 | Farkas | Noémi | Miskolc |

## Magyarázat

A beszúrt rekord bekerült a `sportolo` táblába.

---

# 9. NULL értékek

A `NULL` azt jelenti, hogy nincs megadott érték.

Ez nem ugyanaz, mint az üres szöveg vagy a nulla.

## Syntax

```sql
SELECT oszlopok
FROM tabla
WHERE oszlop IS NULL;
```

vagy:

```sql
SELECT oszlopok
FROM tabla
WHERE oszlop IS NOT NULL;
```

## Példa: sportolók, akiknek nincs email címük

```sql
SELECT vezeteknev, keresztnev, email
FROM sportolo
WHERE email IS NULL;
```

## Végeredmény

| vezeteknev | keresztnev | email |
|---|---|---|
| Szabó | Márk | NULL |
| Kiss | Dávid | NULL |

## Tipikus hiba

Hibás:

```sql
WHERE email = NULL
```

Helyes:

```sql
WHERE email IS NULL
```

---

# 10. UPDATE

Az `UPDATE` meglévő rekord módosítására szolgál.

## Syntax

```sql
UPDATE tabla_neve
SET oszlop = uj_ertek
WHERE feltetel;
```

## Példa

```sql
UPDATE sportolo
SET varos = 'Debrecen'
WHERE id = 8;
```

## Ellenőrző lekérdezés

```sql
SELECT id, vezeteknev, keresztnev, varos
FROM sportolo
WHERE id = 8;
```

## Végeredmény

| id | vezeteknev | keresztnev | varos |
|---:|---|---|---|
| 8 | Farkas | Noémi | Debrecen |

## Fontos

Az `UPDATE` parancsnál a `WHERE` nagyon fontos. Ha kihagyod, minden rekord módosulhat.

Hibás veszélyes példa:

```sql
UPDATE sportolo
SET varos = 'Debrecen';
```

Ez minden sportoló városát Debrecenre állítaná.

---

# 11. DELETE

A `DELETE` rekordok törlésére szolgál.

## Syntax

```sql
DELETE FROM tabla_neve
WHERE feltetel;
```

## Példa

```sql
DELETE FROM sportolo
WHERE id = 8;
```

## Ellenőrző lekérdezés

```sql
SELECT *
FROM sportolo
WHERE id = 8;
```

## Végeredmény

| id | vezeteknev | keresztnev | varos | szuletesi_ev | email |
|---|---|---|---|---|---|

Nincs találat, mert a rekord törlődött.

## Fontos

A `DELETE` parancsnál is kötelező figyelni a `WHERE` feltételre.

Hibás veszélyes példa:

```sql
DELETE FROM sportolo;
```

Ez az egész táblát kiürítené.

---

# 12. SELECT TOP / LIMIT

MySQL/MariaDB környezetben nem `SELECT TOP`, hanem `LIMIT` használatos.

## Syntax

```sql
SELECT oszlopok
FROM tabla
LIMIT darabszam;
```

## Példa: első 3 sportoló

```sql
SELECT vezeteknev, keresztnev
FROM sportolo
LIMIT 3;
```

## Végeredmény

| vezeteknev | keresztnev |
|---|---|
| Kovács | Anna |
| Nagy | Béla |
| Tóth | Kata |

## Példa: három legjobb pontszám

```sql
SELECT sportolo_id, pontszam
FROM jelentkezes
ORDER BY pontszam DESC
LIMIT 3;
```

## Végeredmény

| sportolo_id | pontszam |
|---:|---:|
| 3 | 20 |
| 3 | 19 |
| 1 | 18 |

## Magyarázat

A `LIMIT` önmagában csak levágja az eredményt. Ha „legjobb”, „legnagyobb”, „legkisebb” rekord kell, akkor előtte rendezni kell.

---

# 13. Aggregáló függvények

Az aggregáló függvények több sorból számolnak egyetlen értéket.

## Fontos aggregáló függvények

| Függvény | Jelentés |
|---|---|
| COUNT() | darabszám |
| SUM() | összeg |
| AVG() | átlag |
| MIN() | legkisebb érték |
| MAX() | legnagyobb érték |

---

# 14. MIN

A `MIN()` a legkisebb értéket adja vissza.

## Syntax

```sql
SELECT MIN(oszlop)
FROM tabla;
```

## Példa

```sql
SELECT MIN(pontszam) AS legalacsonyabb_pontszam
FROM jelentkezes;
```

## Végeredmény

| legalacsonyabb_pontszam |
|---:|
| 9 |

---

# 15. MAX

A `MAX()` a legnagyobb értéket adja vissza.

## Syntax

```sql
SELECT MAX(oszlop)
FROM tabla;
```

## Példa

```sql
SELECT MAX(pontszam) AS legmagasabb_pontszam
FROM jelentkezes;
```

## Végeredmény

| legmagasabb_pontszam |
|---:|
| 20 |

## Fontos

Ez csak a pontszámot adja vissza, nem a sportoló nevét. Ha a név is kell, később JOIN vagy al-lekérdezés kell.

---

# 16. COUNT

A `COUNT()` megszámolja a sorokat.

## Syntax

```sql
SELECT COUNT(*)
FROM tabla;
```

## Példa: hány sportoló van?

```sql
SELECT COUNT(*) AS sportolok_szama
FROM sportolo;
```

## Végeredmény

| sportolok_szama |
|---:|
| 7 |

## Példa: hány sportolónak van email címe?

```sql
SELECT COUNT(email) AS emaillel_rendelkezok
FROM sportolo;
```

## Végeredmény

| emaillel_rendelkezok |
|---:|
| 5 |

## Magyarázat

- `COUNT(*)` minden sort számol.
- `COUNT(email)` csak azokat, ahol az email nem NULL.

---

# 17. SUM

A `SUM()` összeadja egy oszlop értékeit.

## Syntax

```sql
SELECT SUM(oszlop)
FROM tabla;
```

## Példa

```sql
SELECT SUM(pontszam) AS osszpontszam
FROM jelentkezes;
```

## Végeredmény

| osszpontszam |
|---:|
| 140 |

---

# 18. AVG

Az `AVG()` átlagot számol.

## Syntax

```sql
SELECT AVG(oszlop)
FROM tabla;
```

## Példa

```sql
SELECT AVG(pontszam) AS atlagpontszam
FROM jelentkezes;
```

## Végeredmény

| atlagpontszam |
|---:|
| 15.5556 |

## Kerekítés

```sql
SELECT ROUND(AVG(pontszam), 2) AS atlagpontszam
FROM jelentkezes;
```

## Végeredmény

| atlagpontszam |
|---:|
| 15.56 |

---

# 19. LIKE

A `LIKE` szövegminták keresésére szolgál.

## Syntax

```sql
SELECT oszlopok
FROM tabla
WHERE oszlop LIKE minta;
```

## Példa: K betűvel kezdődő vezetéknevek

```sql
SELECT vezeteknev, keresztnev
FROM sportolo
WHERE vezeteknev LIKE 'K%';
```

## Végeredmény

| vezeteknev | keresztnev |
|---|---|
| Kovács | Anna |
| Kiss | Dávid |

## Példa: a betűt tartalmazó keresztnevek

```sql
SELECT vezeteknev, keresztnev
FROM sportolo
WHERE keresztnev LIKE '%a%';
```

## Végeredmény

| vezeteknev | keresztnev |
|---|---|
| Kovács | Anna |
| Tóth | Kata |
| Szabó | Márk |
| Varga | Lili |
| Kiss | Dávid |

## Megjegyzés

A kis- és nagybetűk kezelése adatbázis-beállítástól függhet.

---

# 20. Wildcard karakterek

A wildcard karakterek mintakeresésnél használhatók.

## Gyakori wildcardok

| Jel | Jelentés |
|---|---|
| `%` | bármennyi karakter |
| `_` | pontosan egy karakter |

## Példa: pontosan négy karakter hosszú keresztnév, amely A-val kezdődik

```sql
SELECT keresztnev
FROM sportolo
WHERE keresztnev LIKE 'A___';
```

## Végeredmény

| keresztnev |
|---|
| Anna |

## Magyarázat

Az `A___` azt jelenti:

- A betűvel kezdődik,
- utána pontosan három karakter áll.

---

# 21. IN

Az `IN` több lehetséges értéket ad meg egy feltételben.

## Syntax

```sql
SELECT oszlopok
FROM tabla
WHERE oszlop IN (ertek1, ertek2);
```

## Példa

```sql
SELECT vezeteknev, keresztnev, varos
FROM sportolo
WHERE varos IN ('Budapest', 'Pécs');
```

## Végeredmény

| vezeteknev | keresztnev | varos |
|---|---|---|
| Kovács | Anna | Budapest |
| Nagy | Béla | Pécs |
| Tóth | Kata | Budapest |
| Varga | Lili | Pécs |

## Magyarázat

Ez ugyanaz, mint:

```sql
WHERE varos = 'Budapest'
OR varos = 'Pécs'
```

Csak rövidebb és olvashatóbb.

---

# 22. BETWEEN

A `BETWEEN` tartományos szűrésre szolgál.

## Syntax

```sql
SELECT oszlopok
FROM tabla
WHERE oszlop BETWEEN also_ertek AND felso_ertek;
```

## Példa

```sql
SELECT vezeteknev, keresztnev, szuletesi_ev
FROM sportolo
WHERE szuletesi_ev BETWEEN 2005 AND 2006;
```

## Végeredmény

| vezeteknev | keresztnev | szuletesi_ev |
|---|---|---:|
| Kovács | Anna | 2006 |
| Nagy | Béla | 2005 |
| Varga | Lili | 2006 |
| Kiss | Dávid | 2005 |

## Fontos

A `BETWEEN` tartalmazza az alsó és felső határt is.

---

# 23. Aliasok

Aliasokkal átnevezhetjük az oszlopokat vagy táblákat.

## Oszlop alias syntax

```sql
SELECT oszlop AS uj_nev
FROM tabla;
```

## Példa

```sql
SELECT vezeteknev AS vezetek,
       keresztnev AS kereszt
FROM sportolo;
```

## Végeredmény

| vezetek | kereszt |
|---|---|
| Kovács | Anna |
| Nagy | Béla |
| Tóth | Kata |
| Szabó | Márk |
| Varga | Lili |
| Kiss | Dávid |
| Molnár | Eszter |

## Tábla alias példa

```sql
SELECT s.vezeteknev, s.keresztnev
FROM sportolo s;
```

## Magyarázat

Itt a `sportolo` tábla rövid neve `s`.

JOIN-oknál ez nagyon hasznos.

---

# 24. JOIN

A `JOIN` több tábla összekapcsolására szolgál.

Az adatbázisokban az adatok általában nem egyetlen nagy táblában vannak, hanem több kisebb, logikusan összekapcsolt táblában.

## Kapcsolatok a mintaadatbázisban

```text
sportolo.id = jelentkezes.sportolo_id
esemeny.id = jelentkezes.esemeny_id
sportag.id = esemeny.sportag_id
```

## Alap JOIN syntax

```sql
SELECT oszlopok
FROM tabla1
JOIN tabla2
ON tabla1.kulcs = tabla2.idegen_kulcs;
```

---

# 25. INNER JOIN

Az `INNER JOIN` csak azokat a rekordokat jeleníti meg, ahol mindkét táblában van kapcsolódó adat.

## Példa: sportolók és pontszámaik

```sql
SELECT s.vezeteknev,
       s.keresztnev,
       j.pontszam
FROM sportolo s
INNER JOIN jelentkezes j
ON s.id = j.sportolo_id;
```

## Végeredmény

| vezeteknev | keresztnev | pontszam |
|---|---|---:|
| Kovács | Anna | 18 |
| Nagy | Béla | 12 |
| Tóth | Kata | 20 |
| Szabó | Márk | 9 |
| Varga | Lili | 15 |
| Kovács | Anna | 17 |
| Tóth | Kata | 19 |
| Kiss | Dávid | 14 |
| Molnár | Eszter | 16 |

## Miért szerepel Kovács Anna kétszer?

Mert két jelentkezése van.

A JOIN minden kapcsolódó rekordot külön sorban jelenít meg.

---

# 26. LEFT JOIN

A `LEFT JOIN` a bal oldali tábla minden rekordját megjeleníti, akkor is, ha nincs hozzá kapcsolódó adat a jobb oldali táblában.

## Syntax

```sql
SELECT oszlopok
FROM tabla1
LEFT JOIN tabla2
ON tabla1.kulcs = tabla2.idegen_kulcs;
```

## Példa: minden sportoló és a jelentkezései

```sql
SELECT s.vezeteknev,
       s.keresztnev,
       j.esemeny_id,
       j.pontszam
FROM sportolo s
LEFT JOIN jelentkezes j
ON s.id = j.sportolo_id;
```

## Végeredmény

| vezeteknev | keresztnev | esemeny_id | pontszam |
|---|---|---:|---:|
| Kovács | Anna | 1 | 18 |
| Nagy | Béla | 1 | 12 |
| Tóth | Kata | 2 | 20 |
| Szabó | Márk | 3 | 9 |
| Varga | Lili | 2 | 15 |
| Kovács | Anna | 4 | 17 |
| Tóth | Kata | 1 | 19 |
| Kiss | Dávid | 3 | 14 |
| Molnár | Eszter | 5 | 16 |

## Mikor fontos?

Akkor, ha a feladat azt mondja:

- minden sportolót jeleníts meg,
- azok is szerepeljenek, akiknek nincs jelentkezésük,
- keresd meg, kikhez nincs kapcsolódó rekord.

---

# 27. RIGHT JOIN

A `RIGHT JOIN` a jobb oldali tábla minden rekordját megjeleníti.

## Syntax

```sql
SELECT oszlopok
FROM tabla1
RIGHT JOIN tabla2
ON tabla1.kulcs = tabla2.idegen_kulcs;
```

## Példa

```sql
SELECT s.vezeteknev,
       s.keresztnev,
       j.id AS jelentkezes_id
FROM sportolo s
RIGHT JOIN jelentkezes j
ON s.id = j.sportolo_id;
```

## Végeredmény

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

## Érettségi megjegyzés

A `RIGHT JOIN` ritkábban szükséges. Általában ugyanaz megoldható úgy, hogy felcseréled a táblák sorrendjét és `LEFT JOIN`-t használsz.

---

# 28. FULL OUTER JOIN

A `FULL OUTER JOIN` minden rekordot megjelenít mindkét táblából, akkor is, ha nincs párja.

MySQL/MariaDB alatt nincs közvetlen `FULL OUTER JOIN`, ezért általában `LEFT JOIN` és `RIGHT JOIN` kombinációval lehet helyettesíteni.

## Általános syntax más SQL rendszerekben

```sql
SELECT oszlopok
FROM tabla1
FULL OUTER JOIN tabla2
ON tabla1.kulcs = tabla2.idegen_kulcs;
```

## MySQL/MariaDB megoldási elv

```sql
SELECT s.vezeteknev, s.keresztnev, j.id AS jelentkezes_id
FROM sportolo s
LEFT JOIN jelentkezes j
ON s.id = j.sportolo_id

UNION

SELECT s.vezeteknev, s.keresztnev, j.id AS jelentkezes_id
FROM sportolo s
RIGHT JOIN jelentkezes j
ON s.id = j.sportolo_id;
```

## Megjegyzés

Emelt érettségin jellemzően nem ez a legfontosabb JOIN típus. A biztos tudás:

- INNER JOIN
- LEFT JOIN

sokkal fontosabb.

---

# 29. SELF JOIN

A `SELF JOIN` azt jelenti, hogy egy táblát önmagával kapcsolunk össze.

Ehhez a mintaadatbázisba készítsünk egy egyszerűbb táblát.

## Segédtábla

```sql
CREATE TABLE dolgozo (
    id INT PRIMARY KEY,
    nev VARCHAR(100),
    fonok_id INT
);

INSERT INTO dolgozo VALUES
(1, 'Kovács Péter', NULL),
(2, 'Nagy Anna', 1),
(3, 'Tóth Béla', 1),
(4, 'Varga Lili', 2);
```

## dolgozo tábla

| id | nev | fonok_id |
|---:|---|---:|
| 1 | Kovács Péter | NULL |
| 2 | Nagy Anna | 1 |
| 3 | Tóth Béla | 1 |
| 4 | Varga Lili | 2 |

## Példa: dolgozó és főnöke

```sql
SELECT d.nev AS dolgozo,
       f.nev AS fonok
FROM dolgozo d
LEFT JOIN dolgozo f
ON d.fonok_id = f.id;
```

## Végeredmény

| dolgozo | fonok |
|---|---|
| Kovács Péter | NULL |
| Nagy Anna | Kovács Péter |
| Tóth Béla | Kovács Péter |
| Varga Lili | Nagy Anna |

## Magyarázat

Ugyanazt a táblát két néven használjuk:

- `d` = dolgozó
- `f` = főnök

---

# 30. UNION

A `UNION` több lekérdezés eredményét fűzi össze.

## Syntax

```sql
SELECT oszlop
FROM tabla1
UNION
SELECT oszlop
FROM tabla2;
```

## Fontos

A `UNION` eltávolítja az ismétlődő sorokat.

## Példa

```sql
SELECT varos
FROM sportolo
UNION
SELECT helyszin
FROM esemeny;
```

## Végeredmény

| varos |
|---|
| Budapest |
| Pécs |
| Győr |
| Szeged |
| Debrecen |
| Budapest Aréna |
| Pécsi Sportcsarnok |
| Győri pálya |
| Debreceni Uszoda |

## UNION ALL

A `UNION ALL` megtartja az ismétlődéseket is.

```sql
SELECT varos
FROM sportolo
UNION ALL
SELECT helyszin
FROM esemeny;
```

---

# 31. GROUP BY

A `GROUP BY` csoportosításra szolgál.

## Syntax

```sql
SELECT csoportosito_oszlop, COUNT(*)
FROM tabla
GROUP BY csoportosito_oszlop;
```

## Példa: városonként hány sportoló van?

```sql
SELECT varos,
       COUNT(*) AS letszam
FROM sportolo
GROUP BY varos;
```

## Végeredmény

| varos | letszam |
|---|---:|
| Budapest | 2 |
| Debrecen | 1 |
| Győr | 1 |
| Pécs | 2 |
| Szeged | 1 |

## Magyarázat

A `GROUP BY` összevonja az azonos városokat. A `COUNT(*)` pedig megszámolja, hány sportoló tartozik az adott városhoz.

## Érettségi kulcsszavak

Ha ilyet látsz:

- városonként
- eseményenként
- sportáganként
- tanulónként
- kategóriánként

akkor nagy eséllyel `GROUP BY` kell.

---

# 32. HAVING

A `HAVING` csoportok szűrésére szolgál.

## WHERE vs HAVING

| WHERE | HAVING |
|---|---|
| sorokat szűr | csoportokat szűr |
| GROUP BY előtt történik | GROUP BY után történik |
| sima mezőkre használjuk | aggregált értékekre használjuk |

## Syntax

```sql
SELECT csoportosito_oszlop, COUNT(*)
FROM tabla
GROUP BY csoportosito_oszlop
HAVING COUNT(*) > ertek;
```

## Példa: csak azok a városok, ahonnan legalább 2 sportoló van

```sql
SELECT varos,
       COUNT(*) AS letszam
FROM sportolo
GROUP BY varos
HAVING COUNT(*) >= 2;
```

## Végeredmény

| varos | letszam |
|---|---:|
| Budapest | 2 |
| Pécs | 2 |

## Tipikus hiba

Hibás:

```sql
WHERE COUNT(*) >= 2
```

Helyes:

```sql
HAVING COUNT(*) >= 2
```

---

# 33. EXISTS

Az `EXISTS` azt vizsgálja, hogy létezik-e legalább egy kapcsolódó rekord.

## Syntax

```sql
SELECT oszlopok
FROM tabla1
WHERE EXISTS (
    SELECT 1
    FROM tabla2
    WHERE kapcsolat
);
```

## Példa: sportolók, akiknek van jelentkezésük

```sql
SELECT s.vezeteknev, s.keresztnev
FROM sportolo s
WHERE EXISTS (
    SELECT 1
    FROM jelentkezes j
    WHERE j.sportolo_id = s.id
);
```

## Végeredmény

| vezeteknev | keresztnev |
|---|---|
| Kovács | Anna |
| Nagy | Béla |
| Tóth | Kata |
| Szabó | Márk |
| Varga | Lili |
| Kiss | Dávid |
| Molnár | Eszter |

## Magyarázat

A belső lekérdezés azt vizsgálja, hogy az adott sportolóhoz van-e jelentkezés.

---

# 34. ANY / ALL

Az `ANY` és `ALL` összetettebb összehasonlításokra használható. Érettségin ritkább, de érdemes ismerni.

## ANY

Az `ANY` akkor igaz, ha a feltétel legalább egy belső lekérdezésből származó értékre igaz.

## Példa: olyan jelentkezések, ahol a pontszám nagyobb legalább egy nem fizetett jelentkezés pontszámánál

```sql
SELECT id, sportolo_id, pontszam
FROM jelentkezes
WHERE pontszam > ANY (
    SELECT pontszam
    FROM jelentkezes
    WHERE fizetett = 'nem'
);
```

## Végeredmény

| id | sportolo_id | pontszam |
|---:|---:|---:|
| 1 | 1 | 18 |
| 3 | 3 | 20 |
| 5 | 5 | 15 |
| 6 | 1 | 17 |
| 7 | 3 | 19 |
| 9 | 7 | 16 |

## ALL

Az `ALL` akkor igaz, ha a feltétel minden belső értékre igaz.

## Példa: olyan jelentkezések, amelyek pontszáma nagyobb minden nem fizetett jelentkezés pontszámánál

```sql
SELECT id, sportolo_id, pontszam
FROM jelentkezes
WHERE pontszam > ALL (
    SELECT pontszam
    FROM jelentkezes
    WHERE fizetett = 'nem'
);
```

## Végeredmény

| id | sportolo_id | pontszam |
|---:|---:|---:|
| 1 | 1 | 18 |
| 3 | 3 | 20 |
| 6 | 1 | 17 |
| 7 | 3 | 19 |

---

# 35. SELECT INTO / CREATE TABLE AS

MySQL/MariaDB alatt a `SELECT INTO` helyett gyakran `CREATE TABLE AS SELECT` formát használunk.

## Syntax

```sql
CREATE TABLE uj_tabla AS
SELECT oszlopok
FROM regi_tabla;
```

## Példa: budapesti sportolók külön táblába mentése

```sql
CREATE TABLE budapesti_sportolok AS
SELECT id, vezeteknev, keresztnev, varos
FROM sportolo
WHERE varos = 'Budapest';
```

## Ellenőrzés

```sql
SELECT *
FROM budapesti_sportolok;
```

## Végeredmény

| id | vezeteknev | keresztnev | varos |
|---:|---|---|---|
| 1 | Kovács | Anna | Budapest |
| 3 | Tóth | Kata | Budapest |

---

# 36. INSERT INTO SELECT

Az `INSERT INTO SELECT` meglévő adatokból szúr be rekordokat egy másik táblába.

## Syntax

```sql
INSERT INTO cel_tabla (oszlop1, oszlop2)
SELECT oszlop1, oszlop2
FROM forras_tabla
WHERE feltetel;
```

## Példa

```sql
CREATE TABLE pesti_sportolok (
    id INT,
    nev VARCHAR(100),
    varos VARCHAR(50)
);

INSERT INTO pesti_sportolok (id, nev, varos)
SELECT id, CONCAT(vezeteknev, ' ', keresztnev), varos
FROM sportolo
WHERE varos = 'Budapest';
```

## Ellenőrzés

```sql
SELECT *
FROM pesti_sportolok;
```

## Végeredmény

| id | nev | varos |
|---:|---|---|
| 1 | Kovács Anna | Budapest |
| 3 | Tóth Kata | Budapest |

---

# 37. CASE

A `CASE` feltételes logikára szolgál. Olyan, mint Excelben a `HA`.

## Syntax

```sql
SELECT oszlop,
       CASE
           WHEN feltetel THEN eredmeny
           ELSE mas_eredmeny
       END AS uj_oszlop
FROM tabla;
```

## Példa: pontszám minősítése

```sql
SELECT sportolo_id,
       pontszam,
       CASE
           WHEN pontszam >= 18 THEN 'kiváló'
           WHEN pontszam >= 14 THEN 'jó'
           WHEN pontszam >= 10 THEN 'megfelelő'
           ELSE 'gyenge'
       END AS minosites
FROM jelentkezes;
```

## Végeredmény

| sportolo_id | pontszam | minosites |
|---:|---:|---|
| 1 | 18 | kiváló |
| 2 | 12 | megfelelő |
| 3 | 20 | kiváló |
| 4 | 9 | gyenge |
| 5 | 15 | jó |
| 1 | 17 | jó |
| 3 | 19 | kiváló |
| 6 | 14 | jó |
| 7 | 16 | jó |

## Fontos

A feltételek sorrendje számít. Először mindig a legerősebb feltételt írd.

---

# 38. NULL függvények

MySQL/MariaDB alatt gyakori az `IFNULL()` használata.

## Syntax

```sql
IFNULL(oszlop, helyettesito_ertek)
```

## Példa

```sql
SELECT vezeteknev,
       keresztnev,
       IFNULL(email, 'nincs megadva') AS email
FROM sportolo;
```

## Végeredmény

| vezeteknev | keresztnev | email |
|---|---|---|
| Kovács | Anna | anna.kovacs@email.hu |
| Nagy | Béla | bela.nagy@email.hu |
| Tóth | Kata | kata.toth@email.hu |
| Szabó | Márk | nincs megadva |
| Varga | Lili | lili.varga@email.hu |
| Kiss | Dávid | nincs megadva |
| Molnár | Eszter | eszter.molnar@email.hu |

---

# 39. SQL kommentek

Kommenteket magyarázatként írhatunk a kódba.

## Egysoros komment

```sql
-- Ez egy egysoros komment
SELECT *
FROM sportolo;
```

## Többsoros komment

```sql
/*
Ez egy
többsoros komment
*/
SELECT *
FROM sportolo;
```

---

# 40. SQL operátorok

## Aritmetikai operátorok

| Operátor | Jelentés |
|---|---|
| + | összeadás |
| - | kivonás |
| * | szorzás |
| / | osztás |

## Összehasonlító operátorok

| Operátor | Jelentés |
|---|---|
| = | egyenlő |
| <> | nem egyenlő |
| > | nagyobb |
| < | kisebb |
| >= | nagyobb vagy egyenlő |
| <= | kisebb vagy egyenlő |

## Logikai operátorok

| Operátor | Jelentés |
|---|---|
| AND | és |
| OR | vagy |
| NOT | tagadás |
| IN | több lehetséges érték |
| BETWEEN | tartomány |
| LIKE | mintaillesztés |
| EXISTS | létezés vizsgálata |

---

# 41. Allekérdezések

Az allekérdezés egy lekérdezés a lekérdezésen belül. Akkor hasznos, ha egy feltételhez előbb ki kell számolni vagy lekérdezni valamit.

## Alap syntax

```sql
SELECT oszlopok
FROM tabla
WHERE oszlop = (
    SELECT oszlop
    FROM masik_tabla
    WHERE feltetel
);
```

---

## 1. Legmagasabb pontszámot elérő sportoló

```sql
SELECT CONCAT(s.vezeteknev, ' ', s.keresztnev) AS sportolo,
       j.pontszam
FROM sportolo s
JOIN jelentkezes j
ON s.id = j.sportolo_id
WHERE j.pontszam = (
    SELECT MAX(pontszam)
    FROM jelentkezes
);
```

### Végeredmény

| sportolo | pontszam |
|---|---:|
| Tóth Kata | 20 |

---

## 2. Átlag feletti pontszámok

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

---

## 3. Sportolók, akik jelentkeztek eseményre

```sql
SELECT vezeteknev, keresztnev
FROM sportolo
WHERE id IN (
    SELECT sportolo_id
    FROM jelentkezes
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

---

## 4. Sportolók, akik nem jelentkeztek eseményre

```sql
SELECT vezeteknev, keresztnev
FROM sportolo
WHERE id NOT IN (
    SELECT sportolo_id
    FROM jelentkezes
);
```

### Végeredmény

| vezeteknev | keresztnev |
|---|---|

Ebben a mintaadatbázisban nincs ilyen sportoló.

---

## 5. Események, amelyekre budapesti sportoló jelentkezett

```sql
SELECT nev AS esemeny
FROM esemeny
WHERE id IN (
    SELECT esemeny_id
    FROM jelentkezes
    WHERE sportolo_id IN (
        SELECT id
        FROM sportolo
        WHERE varos = 'Budapest'
    )
);
```

### Végeredmény

| esemeny |
|---|
| Tavaszi kupa |
| Városi bajnokság |
| Röplabda nap |

---

## 6. Sportolók, akiknek van átlagnál jobb jelentkezésük

```sql
SELECT DISTINCT CONCAT(s.vezeteknev, ' ', s.keresztnev) AS sportolo
FROM sportolo s
WHERE s.id IN (
    SELECT j.sportolo_id
    FROM jelentkezes j
    WHERE j.pontszam > (
        SELECT AVG(pontszam)
        FROM jelentkezes
    )
);
```

### Végeredmény

| sportolo |
|---|
| Kovács Anna |
| Tóth Kata |
| Molnár Eszter |

---

## 7. Események, ahol a jelentkezők száma nagyobb az átlagos jelentkezésszámnál

```sql
SELECT e.nev AS esemeny,
       COUNT(j.id) AS jelentkezok_szama
FROM esemeny e
JOIN jelentkezes j
ON e.id = j.esemeny_id
GROUP BY e.id, e.nev
HAVING COUNT(j.id) > (
    SELECT AVG(jelentkezes_db)
    FROM (
        SELECT COUNT(*) AS jelentkezes_db
        FROM jelentkezes
        GROUP BY esemeny_id
    ) AS stat
);
```

### Végeredmény

| esemeny | jelentkezok_szama |
|---|---:|
| Tavaszi kupa | 3 |

---

## 8. Sportolók, akik több pontot értek el, mint a saját eseményük átlaga

```sql
SELECT CONCAT(s.vezeteknev, ' ', s.keresztnev) AS sportolo,
       e.nev AS esemeny,
       j.pontszam
FROM jelentkezes j
JOIN sportolo s
ON s.id = j.sportolo_id
JOIN esemeny e
ON e.id = j.esemeny_id
WHERE j.pontszam > (
    SELECT AVG(j2.pontszam)
    FROM jelentkezes j2
    WHERE j2.esemeny_id = j.esemeny_id
);
```

### Végeredmény

| sportolo | esemeny | pontszam |
|---|---|---:|
| Kovács Anna | Tavaszi kupa | 18 |
| Tóth Kata | Városi bajnokság | 20 |
| Kiss Dávid | Nyári fociest | 14 |

---

## 9. Sportolók, akik legalább két eseményre jelentkeztek, és az átlagpontjuk 17 felett van

```sql
SELECT CONCAT(s.vezeteknev, ' ', s.keresztnev) AS sportolo,
       COUNT(j.id) AS jelentkezesek_szama,
       ROUND(AVG(j.pontszam), 2) AS atlagpont
FROM sportolo s
JOIN jelentkezes j
ON s.id = j.sportolo_id
GROUP BY s.id, s.vezeteknev, s.keresztnev
HAVING COUNT(j.id) >= 2
AND AVG(j.pontszam) > (
    SELECT AVG(pontszam)
    FROM jelentkezes
);
```

### Végeredmény

| sportolo | jelentkezesek_szama | atlagpont |
|---|---:|---:|
| Kovács Anna | 2 | 17.50 |
| Tóth Kata | 2 | 19.50 |

---

## 10. Minden sportág legjobb pontszámot elérő sportolója

```sql
SELECT sp.nev AS sportag,
       CONCAT(s.vezeteknev, ' ', s.keresztnev) AS sportolo,
       j.pontszam
FROM sportag sp
JOIN esemeny e
ON sp.id = e.sportag_id
JOIN jelentkezes j
ON e.id = j.esemeny_id
JOIN sportolo s
ON s.id = j.sportolo_id
WHERE j.pontszam = (
    SELECT MAX(j2.pontszam)
    FROM jelentkezes j2
    JOIN esemeny e2
    ON e2.id = j2.esemeny_id
    WHERE e2.sportag_id = sp.id
)
ORDER BY sp.nev;
```

### Végeredmény

| sportag | sportolo | pontszam |
|---|---|---:|
| futball | Kiss Dávid | 14 |
| kézilabda | Tóth Kata | 20 |
| kosárlabda | Tóth Kata | 19 |
| röplabda | Kovács Anna | 17 |
| úszás | Molnár Eszter | 16 |

---

## 11. Eseményenként a legjobb sportoló

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

## 12. Fizetett jelentkezések aránya eseményenként

```sql
SELECT e.nev AS esemeny,
       COUNT(j.id) AS osszes_jelentkezes,
       (
           SELECT COUNT(*)
           FROM jelentkezes j2
           WHERE j2.esemeny_id = e.id
           AND j2.fizetett = 'igen'
       ) AS fizetett_jelentkezes,
       ROUND(
           (
               SELECT COUNT(*)
               FROM jelentkezes j3
               WHERE j3.esemeny_id = e.id
               AND j3.fizetett = 'igen'
           ) / COUNT(j.id) * 100,
           2
       ) AS fizetett_arany_szazalek
FROM esemeny e
JOIN jelentkezes j
ON e.id = j.esemeny_id
GROUP BY e.id, e.nev;
```

### Végeredmény

| esemeny | osszes_jelentkezes | fizetett_jelentkezes | fizetett_arany_szazalek |
|---|---:|---:|---:|
| Tavaszi kupa | 3 | 2 | 66.67 |
| Városi bajnokság | 2 | 1 | 50.00 |
| Nyári fociest | 2 | 2 | 100.00 |
| Röplabda nap | 1 | 1 | 100.00 |
| Őszi úszónap | 1 | 0 | 0.00 |


---

# 42. Érettségi típusfeladatok

Ebben a részben már olyan lekérdezések jönnek, amelyek nagyon hasonlítanak az érettségi adatbázisos gondolkodásához.

---

## 1. feladat: sportolók teljes neve

```sql
SELECT CONCAT(vezeteknev, ' ', keresztnev) AS teljes_nev
FROM sportolo;
```

## Végeredmény

| teljes_nev |
|---|
| Kovács Anna |
| Nagy Béla |
| Tóth Kata |
| Szabó Márk |
| Varga Lili |
| Kiss Dávid |
| Molnár Eszter |

---

## 2. feladat: sportolók teljes neve és városa

```sql
SELECT CONCAT(vezeteknev, ' ', keresztnev) AS teljes_nev,
       varos
FROM sportolo
ORDER BY varos, teljes_nev;
```

## Végeredmény

| teljes_nev | varos |
|---|---|
| Kovács Anna | Budapest |
| Tóth Kata | Budapest |
| Molnár Eszter | Debrecen |
| Szabó Márk | Győr |
| Nagy Béla | Pécs |
| Varga Lili | Pécs |
| Kiss Dávid | Szeged |

---

## 3. feladat: hány különböző városból jöttek sportolók?

```sql
SELECT COUNT(DISTINCT varos) AS varosok_szama
FROM sportolo;
```

## Végeredmény

| varosok_szama |
|---:|
| 5 |

---

## 4. feladat: eseményenként hány jelentkező van?

```sql
SELECT e.nev AS esemeny,
       COUNT(j.id) AS jelentkezok_szama
FROM esemeny e
LEFT JOIN jelentkezes j
ON e.id = j.esemeny_id
GROUP BY e.id, e.nev
ORDER BY jelentkezok_szama DESC;
```

## Végeredmény

| esemeny | jelentkezok_szama |
|---|---:|
| Tavaszi kupa | 3 |
| Városi bajnokság | 2 |
| Nyári fociest | 2 |
| Röplabda nap | 1 |
| Őszi úszónap | 1 |

## Magyarázat

Itt `LEFT JOIN`-t használunk, mert így akkor is megjelenne egy esemény, ha még nem lenne rá jelentkezés.

---

## 5. feladat: sportáganként hány jelentkezés történt?

```sql
SELECT sp.nev AS sportag,
       COUNT(j.id) AS jelentkezesek
FROM sportag sp
JOIN esemeny e
ON sp.id = e.sportag_id
JOIN jelentkezes j
ON e.id = j.esemeny_id
GROUP BY sp.id, sp.nev
ORDER BY jelentkezesek DESC;
```

## Végeredmény

| sportag | jelentkezesek |
|---|---:|
| kosárlabda | 3 |
| kézilabda | 2 |
| futball | 2 |
| röplabda | 1 |
| úszás | 1 |

---

## 6. feladat: kik nem fizettek?

```sql
SELECT DISTINCT CONCAT(s.vezeteknev, ' ', s.keresztnev) AS sportolo
FROM sportolo s
JOIN jelentkezes j
ON s.id = j.sportolo_id
WHERE j.fizetett = 'nem';
```

## Végeredmény

| sportolo |
|---|
| Nagy Béla |
| Varga Lili |
| Molnár Eszter |

---

## 7. feladat: sportolónként átlagpontszám

```sql
SELECT CONCAT(s.vezeteknev, ' ', s.keresztnev) AS sportolo,
       ROUND(AVG(j.pontszam), 2) AS atlagpont
FROM sportolo s
JOIN jelentkezes j
ON s.id = j.sportolo_id
GROUP BY s.id, s.vezeteknev, s.keresztnev
ORDER BY atlagpont DESC;
```

## Végeredmény

| sportolo | atlagpont |
|---|---:|
| Tóth Kata | 19.50 |
| Kovács Anna | 17.50 |
| Molnár Eszter | 16.00 |
| Varga Lili | 15.00 |
| Kiss Dávid | 14.00 |
| Nagy Béla | 12.00 |
| Szabó Márk | 9.00 |

---

# 43. Összetett lekérdezések

Most jönnek azok a feladatok, amelyek már valóban emelt szintű gondolkodást igényelnek.

---

## 1. összetett feladat: ki érte el a legmagasabb pontszámot?

```sql
SELECT CONCAT(s.vezeteknev, ' ', s.keresztnev) AS sportolo,
       e.nev AS esemeny,
       j.pontszam
FROM sportolo s
JOIN jelentkezes j
ON s.id = j.sportolo_id
JOIN esemeny e
ON e.id = j.esemeny_id
WHERE j.pontszam = (
    SELECT MAX(pontszam)
    FROM jelentkezes
);
```

## Végeredmény

| sportolo | esemeny | pontszam |
|---|---|---:|
| Tóth Kata | Városi bajnokság | 20 |

## Magyarázat

A belső lekérdezés:

```sql
SELECT MAX(pontszam)
FROM jelentkezes
```

megkeresi a legnagyobb pontszámot.

A külső lekérdezés megkeresi azt a sportolót és eseményt, ahol ez a pontszám szerepel.

---

## 2. összetett feladat: kik teljesítettek az átlag felett?

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

## Végeredmény

| sportolo | pontszam |
|---|---:|
| Tóth Kata | 20 |
| Tóth Kata | 19 |
| Kovács Anna | 18 |
| Kovács Anna | 17 |
| Molnár Eszter | 16 |

---

## 3. összetett feladat: mely eseményeken van legalább 2 jelentkező?

```sql
SELECT e.nev AS esemeny,
       COUNT(j.id) AS jelentkezok_szama
FROM esemeny e
JOIN jelentkezes j
ON e.id = j.esemeny_id
GROUP BY e.id, e.nev
HAVING COUNT(j.id) >= 2
ORDER BY jelentkezok_szama DESC;
```

## Végeredmény

| esemeny | jelentkezok_szama |
|---|---:|
| Tavaszi kupa | 3 |
| Városi bajnokság | 2 |
| Nyári fociest | 2 |

---

## 4. összetett feladat: sportáganként mennyi a befizetett nevezési díj?

```sql
SELECT sp.nev AS sportag,
       SUM(e.nevezesi_dij) AS befizetett_osszeg
FROM sportag sp
JOIN esemeny e
ON sp.id = e.sportag_id
JOIN jelentkezes j
ON e.id = j.esemeny_id
WHERE j.fizetett = 'igen'
GROUP BY sp.id, sp.nev
ORDER BY befizetett_osszeg DESC;
```

## Végeredmény

| sportag | befizetett_osszeg |
|---|---:|
| kosárlabda | 6000 |
| futball | 4000 |
| röplabda | 2800 |
| kézilabda | 2500 |

## Magyarázat

Itt csak a fizetett jelentkezéseket számoljuk. Ezért szerepel a `WHERE j.fizetett = 'igen'`.

---

## 5. összetett feladat: mely sportolók jelentkeztek egynél több eseményre?

```sql
SELECT CONCAT(s.vezeteknev, ' ', s.keresztnev) AS sportolo,
       COUNT(j.id) AS jelentkezesek_szama
FROM sportolo s
JOIN jelentkezes j
ON s.id = j.sportolo_id
GROUP BY s.id, s.vezeteknev, s.keresztnev
HAVING COUNT(j.id) > 1;
```

## Végeredmény

| sportolo | jelentkezesek_szama |
|---|---:|
| Kovács Anna | 2 |
| Tóth Kata | 2 |

---

## 6. összetett feladat: eseményenként átlagpontszám

```sql
SELECT e.nev AS esemeny,
       ROUND(AVG(j.pontszam), 2) AS atlagpont
FROM esemeny e
JOIN jelentkezes j
ON e.id = j.esemeny_id
GROUP BY e.id, e.nev
ORDER BY atlagpont DESC;
```

## Végeredmény

| esemeny | atlagpont |
|---|---:|
| Városi bajnokság | 17.50 |
| Röplabda nap | 17.00 |
| Őszi úszónap | 16.00 |
| Tavaszi kupa | 16.33 |
| Nyári fociest | 11.50 |

---

## 7. összetett feladat: kiknek nincs megadva email címük, de már jelentkeztek eseményre?

```sql
SELECT DISTINCT CONCAT(s.vezeteknev, ' ', s.keresztnev) AS sportolo
FROM sportolo s
JOIN jelentkezes j
ON s.id = j.sportolo_id
WHERE s.email IS NULL;
```

## Végeredmény

| sportolo |
|---|
| Szabó Márk |
| Kiss Dávid |

---

## 8. összetett feladat: események telítettsége százalékban

```sql
SELECT e.nev AS esemeny,
       e.max_letszam,
       COUNT(j.id) AS jelentkezok,
       ROUND(COUNT(j.id) / e.max_letszam * 100, 2) AS telitettseg_szazalek
FROM esemeny e
LEFT JOIN jelentkezes j
ON e.id = j.esemeny_id
GROUP BY e.id, e.nev, e.max_letszam
ORDER BY telitettseg_szazalek DESC;
```

## Végeredmény

| esemeny | max_letszam | jelentkezok | telitettseg_szazalek |
|---|---:|---:|---:|
| Őszi úszónap | 12 | 1 | 8.33 |
| Városi bajnokság | 16 | 2 | 12.50 |
| Röplabda nap | 18 | 1 | 5.56 |
| Tavaszi kupa | 20 | 3 | 15.00 |
| Nyári fociest | 22 | 2 | 9.09 |

## Megjegyzés

A sorrend itt a százalék alapján van. Ha pontosan csökkenő sorrendet szeretnénk, az `ORDER BY telitettseg_szazalek DESC` ezt biztosítja.

---

## 9. összetett feladat: minden esemény neve, sportága és jelentkezőinek száma

```sql
SELECT e.nev AS esemeny,
       sp.nev AS sportag,
       COUNT(j.id) AS jelentkezok_szama
FROM esemeny e
JOIN sportag sp
ON sp.id = e.sportag_id
LEFT JOIN jelentkezes j
ON e.id = j.esemeny_id
GROUP BY e.id, e.nev, sp.nev
ORDER BY e.datum;
```

## Végeredmény

| esemeny | sportag | jelentkezok_szama |
|---|---|---:|
| Tavaszi kupa | kosárlabda | 3 |
| Városi bajnokság | kézilabda | 2 |
| Röplabda nap | röplabda | 1 |
| Nyári fociest | futball | 2 |
| Őszi úszónap | úszás | 1 |

---

## 10. összetett feladat: nem fizetett nevezések eseményenként

```sql
SELECT e.nev AS esemeny,
       COUNT(j.id) AS nem_fizetett_db
FROM esemeny e
JOIN jelentkezes j
ON e.id = j.esemeny_id
WHERE j.fizetett = 'nem'
GROUP BY e.id, e.nev
ORDER BY nem_fizetett_db DESC;
```

## Végeredmény

| esemeny | nem_fizetett_db |
|---|---:|
| Városi bajnokság | 1 |
| Tavaszi kupa | 1 |
| Őszi úszónap | 1 |

---

# Tipikus érettségi hibák

## 1. WHERE helyett HAVING kellene

Hibás:

```sql
SELECT varos, COUNT(*)
FROM sportolo
WHERE COUNT(*) >= 2
GROUP BY varos;
```

Helyes:

```sql
SELECT varos, COUNT(*)
FROM sportolo
GROUP BY varos
HAVING COUNT(*) >= 2;
```

---

## 2. Rossz JOIN kapcsolat

Hibás:

```sql
SELECT s.vezeteknev, e.nev
FROM sportolo s
JOIN esemeny e
ON s.id = e.id;
```

Miért hibás?

A `sportolo` és az `esemeny` között nincs közvetlen kapcsolat.

Helyes:

```sql
SELECT s.vezeteknev, e.nev
FROM sportolo s
JOIN jelentkezes j
ON s.id = j.sportolo_id
JOIN esemeny e
ON e.id = j.esemeny_id;
```

---

## 3. DISTINCT rossz használata

Hibás, ha csak városokat kérnek:

```sql
SELECT DISTINCT varos, vezeteknev
FROM sportolo;
```

Helyes:

```sql
SELECT DISTINCT varos
FROM sportolo;
```

---

## 4. NULL rossz vizsgálata

Hibás:

```sql
WHERE email = NULL
```

Helyes:

```sql
WHERE email IS NULL
```

---

## 5. LIKE helyett egyenlőségjel

Hibás:

```sql
WHERE vezeteknev = 'K%'
```

Helyes:

```sql
WHERE vezeteknev LIKE 'K%'
```

---

# Záró gondolat

Az SQL-ben a legfontosabb nem az, hogy bemagold a parancsokat, hanem hogy megtanuld kérdésenként felépíteni a gondolkodást.

Egy jó adatbázisos megoldás mindig innen indul:

1. Mit kér a feladat?
2. Melyik táblában van az adat?
3. Kell-e másik tábla?
4. Ha igen, mi a kapcsolat?
5. Kell-e szűrés?
6. Kell-e csoportosítás?
7. Kell-e csoportokra szűrés?
8. Kell-e rendezés?
9. Kell-e csak az első néhány rekord?

Ha ezt a gondolkodást begyakorlod, az adatbázis-kezelés az emelt digitális kultúra érettségi egyik legbiztosabb pontszerző része lehet.
