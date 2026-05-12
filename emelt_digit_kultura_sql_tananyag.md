# Emelt szintű digitális kultúra érettségi – SQL és adatbázis-kezelés

## Bevezetés

Az SQL és az adatbázis-kezelés az emelt szintű digitális kultúra érettségi egyik legfontosabb témaköre. A feladatok elsőre bonyolultnak tűnhetnek, de valójában ugyanazok a minták ismétlődnek újra és újra.

A siker kulcsa nem a parancsok bemagolása, hanem annak megértése:

- melyik adat melyik táblában található,
- hogyan kapcsolódnak a táblák,
- mikor kell szűrni,
- mikor kell csoportosítani,
- és hogyan lehet a feladat szövegét SQL-gondolkodásra lefordítani.

Ez a tananyag:
- a magyar emelt digitális kultúra követelményrendszerére épül,
- a W3Schools logikáját követi,
- MySQL/MariaDB környezetre készült,
- és minden témához:
  - syntaxot,
  - egyszerű példát,
  - érettségi szintű példát,
  - eredménytáblát,
  - rövid magyarázatot tartalmaz.

---

# Tartalomjegyzék

1. SQL Syntax
2. Adatbázis-alapfogalmak
3. Adatbázis létrehozása
4. CREATE TABLE
5. Adattípusok
6. PRIMARY KEY és FOREIGN KEY
7. INSERT INTO
8. SELECT
9. DISTINCT
10. WHERE
11. ORDER BY
12. AND / OR / NOT
13. UPDATE
14. DELETE
15. NULL
16. MIN / MAX
17. COUNT / SUM / AVG
18. LIKE
19. Wildcardok
20. IN
21. BETWEEN
22. Aliasok
23. CONCAT
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
36. Tipikus hibák

---

# 1. SQL Syntax

## Alap syntax

```sql
SELECT oszlopok
FROM tabla
WHERE feltetel
ORDER BY oszlop;
```

---

## Egyszerű példa

```sql
SELECT vezeteknev, keresztnev
FROM sportolo;
```

---

## Érettségi szintű példa

### Feladat

Listázd ki a budapesti sportolók nevét ABC sorrendben.

```sql
SELECT vezeteknev, keresztnev
FROM sportolo
WHERE varos = 'Budapest'
ORDER BY vezeteknev, keresztnev;
```

---

## Végeredmény

| vezeteknev | keresztnev |
|---|---|
| Kovács | Anna |
| Tóth | Kata |

---

# 2. Adatbázis-alapfogalmak

---

# Adatbázis

Egymással kapcsolatban álló adatok szervezett gyűjteménye.

---

# Tábla

Az adatokat táblákban tároljuk.

Példák:
- sportolo
- esemeny
- jelentkezes

---

# Rekord

A tábla egy sora.

| id | nev |
|---:|---|
| 1 | Kovács Anna |

---

# Mező

A tábla egy oszlopa.

Példák:
- nev
- varos
- pontszam

---

# PRIMARY KEY

Egyedi azonosító.

```sql
id INT PRIMARY KEY
```

---

# FOREIGN KEY

Kapcsolat két tábla között.

```sql
FOREIGN KEY (sportolo_id)
REFERENCES sportolo(id)
```

---

# 3. Adatbázis létrehozása

---

## Syntax

```sql
CREATE DATABASE adatbazis_nev;
```

---

## Példa

```sql
CREATE DATABASE sportverseny;
```

---

## Adatbázis kiválasztása

```sql
USE sportverseny;
```

---

# 4. CREATE TABLE

---

## Syntax

```sql
CREATE TABLE tabla_nev (
    oszlop adattipus
);
```

---

## Egyszerű példa

```sql
CREATE TABLE sportolo (
    id INT PRIMARY KEY,
    nev VARCHAR(100)
);
```

---

## Érettségi szintű példa

```sql
CREATE TABLE jelentkezes (
    id INT PRIMARY KEY,
    sportolo_id INT,
    esemeny_id INT,
    pontszam INT,
    fizetett VARCHAR(10),
    FOREIGN KEY (sportolo_id)
        REFERENCES sportolo(id),
    FOREIGN KEY (esemeny_id)
        REFERENCES esemeny(id)
);
```

---

# 5. Adattípusok

| Típus | Jelentés |
|---|---|
| INT | egész szám |
| VARCHAR | szöveg |
| DATE | dátum |
| DOUBLE | valós szám |
| BOOLEAN | logikai érték |

---

## Példa

```sql
CREATE TABLE esemeny (
    id INT,
    nev VARCHAR(100),
    datum DATE
);
```

---

# 6. PRIMARY KEY és FOREIGN KEY

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

## Kapcsolati séma

```text
sportolo
    |
jelentkezes
    |
esemeny
```

---

# 7. INSERT INTO

---

## Syntax

```sql
INSERT INTO tabla
VALUES (...);
```

---

## Egyszerű példa

```sql
INSERT INTO sportolo
VALUES (1, 'Kovács', 'Anna');
```

---

## Érettségi szintű példa

```sql
INSERT INTO jelentkezes
VALUES (1, 1, 2, 18, 'igen');
```

---

# 8. SELECT

---

## Syntax

```sql
SELECT oszlopok
FROM tabla;
```

---

## Egyszerű példa

```sql
SELECT *
FROM sportolo;
```

---

## Érettségi szintű példa

### Feladat

Listázd ki a sportolók nevét és városát.

```sql
SELECT vezeteknev,
       keresztnev,
       varos
FROM sportolo;
```

---

## Végeredmény

| vezeteknev | keresztnev | varos |
|---|---|---|
| Kovács | Anna | Budapest |

---

# 9. DISTINCT

---

## Syntax

```sql
SELECT DISTINCT oszlop
FROM tabla;
```

---

## Egyszerű példa

```sql
SELECT DISTINCT varos
FROM sportolo;
```

---

## Végeredmény

| varos |
|---|
| Budapest |
| Pécs |

---

## Érettségi szintű példa

### Feladat

Add meg, hány különböző városból érkeztek sportolók.

```sql
SELECT COUNT(DISTINCT varos)
FROM sportolo;
```

---

## Végeredmény

| COUNT(DISTINCT varos) |
|---:|
| 5 |

---

# 10. WHERE

---

## Syntax

```sql
SELECT *
FROM tabla
WHERE feltetel;
```

---

## Egyszerű példa

```sql
SELECT *
FROM sportolo
WHERE varos = 'Budapest';
```

---

## Érettségi szintű példa

### Feladat

Listázd ki a 2006 után született budapesti sportolókat.

```sql
SELECT vezeteknev,
       keresztnev
FROM sportolo
WHERE varos = 'Budapest'
AND szuletesi_ev > 2006;
```

---

## Végeredmény

| vezeteknev | keresztnev |
|---|---|
| Tóth | Kata |

---

# 11. ORDER BY

---

## Syntax

```sql
ORDER BY oszlop ASC
```

vagy

```sql
ORDER BY oszlop DESC
```

---

## Egyszerű példa

```sql
SELECT *
FROM sportolo
ORDER BY vezeteknev;
```

---

## Érettségi szintű példa

### Feladat

Listázd ki a három legjobb pontszámot.

```sql
SELECT sportolo_id,
       pontszam
FROM jelentkezes
ORDER BY pontszam DESC
LIMIT 3;
```

---

## Végeredmény

| sportolo_id | pontszam |
|---:|---:|
| 3 | 20 |
| 3 | 19 |
| 1 | 18 |

---

# 12. AND / OR / NOT

---

## Egyszerű példa

```sql
SELECT *
FROM sportolo
WHERE varos = 'Budapest'
AND szuletesi_ev = 2006;
```

---

## Érettségi szintű példa

### Feladat

Listázd ki a budapesti vagy pécsi sportolókat, akik 2005 után születtek.

```sql
SELECT vezeteknev,
       keresztnev,
       varos
FROM sportolo
WHERE (
    varos = 'Budapest'
    OR varos = 'Pécs'
)
AND szuletesi_ev > 2005;
```

---

# 13. UPDATE

---

## Syntax

```sql
UPDATE tabla
SET oszlop = ertek
WHERE feltetel;
```

---

## Egyszerű példa

```sql
UPDATE sportolo
SET varos = 'Debrecen'
WHERE id = 1;
```

---

## Érettségi szintű példa

### Feladat

Állítsd át az összes nem fizetett jelentkezést „függőben” állapotúra.

```sql
UPDATE jelentkezes
SET fizetett = 'függőben'
WHERE fizetett = 'nem';
```

---

# 14. DELETE

---

## Syntax

```sql
DELETE FROM tabla
WHERE feltetel;
```

---

## Egyszerű példa

```sql
DELETE FROM sportolo
WHERE id = 7;
```

---

## Érettségi szintű példa

### Feladat

Töröld azokat a jelentkezéseket, ahol a pontszám 10 alatt van.

```sql
DELETE FROM jelentkezes
WHERE pontszam < 10;
```

---

# 15. NULL

---

## Egyszerű példa

```sql
SELECT *
FROM sportolo
WHERE email IS NULL;
```

---

## Végeredmény

| vezeteknev | keresztnev |
|---|---|
| Szabó | Márk |

---

## Érettségi szintű példa

### Feladat

Listázd ki azokat a sportolókat, akiknek nincs email címe, de már jelentkeztek eseményre.

```sql
SELECT DISTINCT s.vezeteknev,
                s.keresztnev
FROM sportolo s
JOIN jelentkezes j
ON s.id = j.sportolo_id
WHERE s.email IS NULL;
```

---

# 16. MIN / MAX

---

## Egyszerű példa

```sql
SELECT MAX(pontszam)
FROM jelentkezes;
```

---

## Végeredmény

| MAX(pontszam) |
|---:|
| 20 |

---

## Érettségi szintű példa

### Feladat

Add meg eseményenként a legmagasabb pontszámot.

```sql
SELECT esemeny_id,
       MAX(pontszam) AS maxpont
FROM jelentkezes
GROUP BY esemeny_id;
```

---

# 17. COUNT / SUM / AVG

---

## Egyszerű példa

```sql
SELECT AVG(pontszam)
FROM jelentkezes;
```

---

## Érettségi szintű példa

### Feladat

Add meg sportáganként az átlagpontszámot.

```sql
SELECT sp.nev,
       ROUND(AVG(j.pontszam), 2) AS atlag
FROM sportag sp
JOIN esemeny e
ON sp.id = e.sportag_id
JOIN jelentkezes j
ON e.id = j.esemeny_id
GROUP BY sp.id, sp.nev;
```

---

# 18. LIKE

---

## Egyszerű példa

```sql
SELECT *
FROM sportolo
WHERE vezeteknev LIKE 'K%';
```

---

## Érettségi szintű példa

### Feladat

Listázd ki azokat a sportolókat, akiknek a keresztneve tartalmazza az „a” betűt.

```sql
SELECT vezeteknev,
       keresztnev
FROM sportolo
WHERE keresztnev LIKE '%a%';
```

---

# 19. Wildcardok

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

---

# 20. IN

---

## Egyszerű példa

```sql
SELECT *
FROM sportolo
WHERE varos IN ('Budapest', 'Pécs');
```

---

## Érettségi szintű példa

### Feladat

Listázd ki azokat az eseményeket, amelyek Budapesten vagy Győrben kerülnek megrendezésre.

```sql
SELECT nev,
       helyszin
FROM esemeny
WHERE helyszin IN (
    'Budapest Aréna',
    'Győri pálya'
);
```

---

# 21. BETWEEN

---

## Egyszerű példa

```sql
SELECT *
FROM sportolo
WHERE szuletesi_ev
BETWEEN 2005 AND 2006;
```

---

## Érettségi szintű példa

### Feladat

Listázd ki a 2026 április és június közötti eseményeket.

```sql
SELECT nev,
       datum
FROM esemeny
WHERE datum
BETWEEN '2026-04-01'
AND '2026-06-30';
```

---

# 22. Aliasok

---

## Egyszerű példa

```sql
SELECT vezeteknev AS vezetek
FROM sportolo;
```

---

## Érettségi szintű példa

```sql
SELECT CONCAT(
    vezeteknev,
    ' ',
    keresztnev
) AS teljes_nev
FROM sportolo;
```

---

# 23. CONCAT

---

## Egyszerű példa

```sql
SELECT CONCAT(
    vezeteknev,
    ' ',
    keresztnev
)
FROM sportolo;
```

---

## Érettségi szintű példa

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

---

# 24. Dátumkezelés

A dátumfüggvények nagyon fontosak, mert az érettségin gyakran szerepelnek:
- események,
- időpontok,
- időintervallumok,
- születési dátumok.

---

# YEAR()

## Syntax

```sql
YEAR(datum)
```

---

## Egyszerű példa

```sql
SELECT YEAR(datum)
FROM esemeny;
```

---

## Érettségi szintű példa

### Feladat

Listázd ki a 2026-os eseményeket.

```sql
SELECT nev,
       datum
FROM esemeny
WHERE YEAR(datum) = 2026;
```

---

# MONTH()

---

## Egyszerű példa

```sql
SELECT MONTH(datum)
FROM esemeny;
```

---

## Érettségi szintű példa

### Feladat

Listázd ki a májusi eseményeket.

```sql
SELECT nev,
       datum
FROM esemeny
WHERE MONTH(datum) = 5;
```

---

# DAY()

---

## Egyszerű példa

```sql
SELECT DAY(datum)
FROM esemeny;
```

---

## Érettségi szintű példa

### Feladat

Listázd ki azokat az eseményeket, amelyeket hónap 15-én rendeznek.

```sql
SELECT nev,
       datum
FROM esemeny
WHERE DAY(datum) = 15;
```

---

# DATEDIFF()

Két dátum különbsége napokban.

---

## Egyszerű példa

```sql
SELECT DATEDIFF(
    '2026-06-01',
    '2026-05-20'
);
```

---

## Végeredmény

| DATEDIFF |
|---:|
| 12 |

---

## Érettségi szintű példa

### Feladat

Add meg, hány nap múlva lesznek az események 2026-03-01-hez képest.

```sql
SELECT nev,
       datum,
       DATEDIFF(
           datum,
           '2026-03-01'
       ) AS napok
FROM esemeny;
```

---

# NOW()

Aktuális dátum és idő.

```sql
SELECT NOW();
```

---

# CURDATE()

Aktuális dátum.

```sql
SELECT CURDATE();
```

---

# DATE_FORMAT()

Dátum formázása.

---

## Példa

```sql
SELECT DATE_FORMAT(
    datum,
    '%Y.%m.%d'
)
FROM esemeny;
```

---

# 25. CASE

---

## Egyszerű példa

```sql
SELECT pontszam,
       CASE
           WHEN pontszam >= 18
           THEN 'kiváló'
           ELSE 'egyéb'
       END AS minosites
FROM jelentkezes;
```

---

## Érettségi szintű példa

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

---

# 26. JOIN

---

## Egyszerű példa

```sql
SELECT s.vezeteknev,
       j.pontszam
FROM sportolo s
JOIN jelentkezes j
ON s.id = j.sportolo_id;
```

---

## Érettségi szintű példa

### Feladat

Listázd ki a sportoló nevét, az esemény nevét és a pontszámot.

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

---

# 27. LEFT JOIN

---

## Egyszerű példa

```sql
SELECT s.vezeteknev,
       j.id
FROM sportolo s
LEFT JOIN jelentkezes j
ON s.id = j.sportolo_id;
```

---

## Érettségi szintű példa

### Feladat

Listázd ki azokat a sportolókat, akik nem jelentkeztek eseményre.

```sql
SELECT s.vezeteknev,
       s.keresztnev
FROM sportolo s
LEFT JOIN jelentkezes j
ON s.id = j.sportolo_id
WHERE j.id IS NULL;
```

---

# 28. GROUP BY

---

## Egyszerű példa

```sql
SELECT varos,
       COUNT(*)
FROM sportolo
GROUP BY varos;
```

---

## Érettségi szintű példa

### Feladat

Add meg sportáganként a jelentkezések számát.

```sql
SELECT sp.nev,
       COUNT(j.id) AS jelentkezesek
FROM sportag sp
JOIN esemeny e
ON sp.id = e.sportag_id
JOIN jelentkezes j
ON e.id = j.esemeny_id
GROUP BY sp.id, sp.nev;
```

---

# 29. HAVING

---

## Egyszerű példa

```sql
SELECT varos,
       COUNT(*)
FROM sportolo
GROUP BY varos
HAVING COUNT(*) >= 2;
```

---

## Érettségi szintű példa

### Feladat

Listázd ki azokat az eseményeket, amelyekre legalább 2 jelentkezés érkezett.

```sql
SELECT e.nev,
       COUNT(j.id) AS db
FROM esemeny e
JOIN jelentkezes j
ON e.id = j.esemeny_id
GROUP BY e.id, e.nev
HAVING COUNT(j.id) >= 2;
```

---

# 30. Függvénykombinációk

---

## Egyszerű példa

```sql
SELECT ROUND(
    AVG(pontszam),
    2
)
FROM jelentkezes;
```

---

## Érettségi szintű példa

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

---

# 31. Allekérdezések

---

## Egyszerű példa

```sql
SELECT *
FROM jelentkezes
WHERE pontszam = (
    SELECT MAX(pontszam)
    FROM jelentkezes
);
```

---

## Érettségi szintű példa

### Feladat

Listázd ki azokat a sportolókat, akik az átlag felett teljesítettek.

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

---

# 32. EXISTS

---

## Egyszerű példa

```sql
SELECT *
FROM sportolo s
WHERE EXISTS (
    SELECT 1
    FROM jelentkezes j
    WHERE j.sportolo_id = s.id
);
```

---

## Érettségi szintű példa

### Feladat

Listázd ki azokat az eseményeket, amelyekre érkezett jelentkezés.

```sql
SELECT *
FROM esemeny e
WHERE EXISTS (
    SELECT 1
    FROM jelentkezes j
    WHERE j.esemeny_id = e.id
);
```

---

# 33. CREATE TABLE AS

---

## Egyszerű példa

```sql
CREATE TABLE budapestiek AS
SELECT *
FROM sportolo
WHERE varos = 'Budapest';
```

---

## Érettségi szintű példa

```sql
CREATE TABLE legjobbak AS
SELECT sportolo_id,
       pontszam
FROM jelentkezes
WHERE pontszam >= 18;
```

---

# 34. INSERT INTO SELECT

---

## Egyszerű példa

```sql
INSERT INTO budapestiek
SELECT *
FROM sportolo
WHERE varos = 'Budapest';
```

---

## Érettségi szintű példa

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

# 35. Összetett érettségi feladatok

---

# 1. feladat

### Feladat

Add meg eseményenként a legjobb sportoló nevét és pontszámát.

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

---

# 2. feladat

### Feladat

Listázd ki sportáganként az átlagpontszámot, de csak azokat, ahol az átlag nagyobb 15-nél.

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

---

# 36. Tipikus hibák

---

# NULL hibás kezelése

HIBÁS:

```sql
WHERE email = NULL
```

HELYES:

```sql
WHERE email IS NULL
```

---

# COUNT használata WHERE-ben

HIBÁS:

```sql
WHERE COUNT(*) > 2
```

HELYES:

```sql
HAVING COUNT(*) > 2
```

---

# Rossz JOIN

HIBÁS:

```sql
ON sportolo.id = esemeny.id
```

Mindig a kapcsolódó mezőket kell összekötni.

---

# LIKE helyett =

HIBÁS:

```sql
WHERE nev = 'K%'
```

HELYES:

```sql
WHERE nev LIKE 'K%'
```

---

# Záró gondolat


A legtöbb feladat ugyanarra a néhány mintára épül:
- JOIN
- GROUP BY
- HAVING
- allekérdezés
- aggregáló függvények
