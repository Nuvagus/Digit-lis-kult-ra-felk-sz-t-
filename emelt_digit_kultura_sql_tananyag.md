# Emelt szintű digitális kultúra érettségi – SQL és adatbázis-kezelés

## Bevezetés

Az emelt szintű digitális kultúra érettségi adatbázis-kezelés része elsőre ijesztőnek tűnhet, de valójában az egyik leglogikusabban felépülő témakör. Az SQL-ben ugyanazok a minták ismétlődnek újra és újra. Ha ezeket felismered, nagyon stabil pontokat lehet szerezni.

A legtöbb hibát nem az okozza, hogy valaki „nem tud SQL-t”, hanem:
- rosszul értelmezi a feladatot,
- nem látja maga előtt az eredménytáblát,
- vagy nem érti a táblák közötti kapcsolatokat.

Ezért ebben a tananyagban:
- minden SQL-rész után megjelenik a végeredmény,
- minden lekérdezéshez tartozik magyarázat,
- és végig ugyanazt a mintaadatbázist használjuk.

A felépítés a W3Schools SQL tutorial logikáját követi, mert az nagyon jól tanítható struktúrában halad:
1. syntax
2. SELECT
3. WHERE
4. ORDER BY
5. AND / OR / NOT
6. INSERT / UPDATE / DELETE
7. aggregáló függvények
8. GROUP BY
9. JOIN
10. stb.

---

# Tartalomjegyzék

1. SQL Syntax
2. Adatbázis létrehozása
3. SELECT
4. SELECT DISTINCT
5. WHERE
6. AND / OR / NOT
7. ORDER BY
8. INSERT INTO
9. UPDATE
10. DELETE
11. MIN és MAX
12. COUNT
13. SUM
14. AVG
15. LIKE
16. IN
17. BETWEEN
18. Aliasok
19. JOIN
20. LEFT JOIN
21. GROUP BY
22. HAVING
23. EXISTS
24. CONCAT
25. CASE WHEN
26. NULL kezelés
27. Dátumfüggvények
28. Szövegfüggvények
29. Al-lekérdezések
30. Tipikus érettségi hibák

---

# 1. SQL Syntax

Minden SQL lekérdezés ugyanarra az alaplogikára épül.

Az SQL kulcsszavai:
- SELECT
- FROM
- WHERE
- GROUP BY
- ORDER BY

általában nagybetűvel szerepelnek, bár az SQL nem érzékeny a kis- és nagybetűkre.

---

## Egy alap SQL lekérdezés

```sql
SELECT oszlopnev
FROM tablanev;
```

---

## Példa

```sql
SELECT vezeteknev
FROM sportolo;
```

---

## Mit jelent?

- `SELECT` → mit szeretnénk lekérni
- `FROM` → melyik táblából

---

# 2. Adatbázis létrehozása

A teljes tananyag során ezt a mintaadatbázist használjuk.

---

## Adatbázis létrehozása

```sql
CREATE DATABASE sportverseny;
```

---

## Adatbázis kiválasztása

```sql
USE sportverseny;
```

---

# Táblák létrehozása

## sportolo

```sql
CREATE TABLE sportolo (
    id INT PRIMARY KEY,
    vezeteknev VARCHAR(50),
    keresztnev VARCHAR(50),
    varos VARCHAR(50),
    szuletesi_ev INT
);
```

---

## sportag

```sql
CREATE TABLE sportag (
    id INT PRIMARY KEY,
    nev VARCHAR(50)
);
```

---

## esemeny

```sql
CREATE TABLE esemeny (
    id INT PRIMARY KEY,
    nev VARCHAR(100),
    sportag_id INT,
    helyszin VARCHAR(100),
    datum DATE,
    max_letszam INT
);
```

---

## jelentkezes

```sql
CREATE TABLE jelentkezes (
    id INT PRIMARY KEY,
    sportolo_id INT,
    esemeny_id INT,
    pontszam INT,
    fizetett VARCHAR(10)
);
```

---

# Adatok feltöltése

## sportolo

```sql
INSERT INTO sportolo VALUES
(1, 'Kovács', 'Anna', 'Budapest', 2006),
(2, 'Nagy', 'Béla', 'Pécs', 2005),
(3, 'Tóth', 'Kata', 'Budapest', 2007),
(4, 'Szabó', 'Márk', 'Győr', 2004),
(5, 'Varga', 'Lili', 'Pécs', 2006);
```

---

## A sportolo tábla tartalma

| id | vezeteknev | keresztnev | varos | szuletesi_ev |
|---:|---|---|---|---:|
| 1 | Kovács | Anna | Budapest | 2006 |
| 2 | Nagy | Béla | Pécs | 2005 |
| 3 | Tóth | Kata | Budapest | 2007 |
| 4 | Szabó | Márk | Győr | 2004 |
| 5 | Varga | Lili | Pécs | 2006 |

---

# 3. SELECT

A SELECT az SQL legfontosabb parancsa.

Ezzel választjuk ki, milyen adatokat szeretnénk megjeleníteni.

---

## Minden oszlop lekérdezése

```sql
SELECT *
FROM sportolo;
```

---

## Végeredmény

| id | vezeteknev | keresztnev | varos | szuletesi_ev |
|---:|---|---|---|---:|
| 1 | Kovács | Anna | Budapest | 2006 |
| 2 | Nagy | Béla | Pécs | 2005 |
| 3 | Tóth | Kata | Budapest | 2007 |
| 4 | Szabó | Márk | Győr | 2004 |
| 5 | Varga | Lili | Pécs | 2006 |

---

## Mit jelent a * ?

A `*` jelentése:
„minden oszlop”.

---

## Csak bizonyos oszlopok

```sql
SELECT vezeteknev, keresztnev
FROM sportolo;
```

---

## Végeredmény

| vezeteknev | keresztnev |
|---|---|
| Kovács | Anna |
| Nagy | Béla |
| Tóth | Kata |
| Szabó | Márk |
| Varga | Lili |

---

# Érettségi tipp

Ha a feladat pontosan megadja a szükséges oszlopokat, ne használj `*`-ot.

---

# 4. SELECT DISTINCT

A DISTINCT eltávolítja az ismétlődő értékeket.

---

## Városok ismétlődés nélkül

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
| Győr |

---

## Mi történt?

Az eredeti táblában:
- Budapest kétszer,
- Pécs kétszer

szerepelt.

A DISTINCT csak az egyedi értékeket hagyta meg.

---

# Tipikus érettségi kulcsszavak

- különböző
- egyedi
- ismétlődés nélkül

---

# 5. WHERE

A WHERE szűrésre szolgál.

---

## Budapesti sportolók

```sql
SELECT *
FROM sportolo
WHERE varos = 'Budapest';
```

---

## Végeredmény

| id | vezeteknev | keresztnev | varos | szuletesi_ev |
|---:|---|---|---|---:|
| 1 | Kovács | Anna | Budapest | 2006 |
| 3 | Tóth | Kata | Budapest | 2007 |

---

## Mit csinált a WHERE?

A WHERE csak azokat a rekordokat hagyta meg,
ahol a `varos` mező értéke Budapest.

---

## 2006 után születettek

```sql
SELECT *
FROM sportolo
WHERE szuletesi_ev > 2006;
```

---

## Végeredmény

| id | vezeteknev | keresztnev | varos | szuletesi_ev |
|---:|---|---|---|---:|
| 3 | Tóth | Kata | Budapest | 2007 |

---

# Operátorok

| Operátor | Jelentés |
|---|---|
| = | egyenlő |
| <> | nem egyenlő |
| > | nagyobb |
| < | kisebb |
| >= | nagyobb vagy egyenlő |
| <= | kisebb vagy egyenlő |

---

# 6. AND / OR / NOT

---

# AND

Mindkét feltételnek teljesülnie kell.

```sql
SELECT *
FROM sportolo
WHERE varos = 'Budapest'
AND szuletesi_ev = 2006;
```

---

## Végeredmény

| id | vezeteknev | keresztnev | varos | szuletesi_ev |
|---:|---|---|---|---:|
| 1 | Kovács | Anna | Budapest | 2006 |

---

# OR

Legalább az egyik feltétel teljesül.

```sql
SELECT *
FROM sportolo
WHERE varos = 'Budapest'
OR varos = 'Pécs';
```

---

## Végeredmény

| id | vezeteknev | keresztnev | varos | szuletesi_ev |
|---:|---|---|---|---:|
| 1 | Kovács | Anna | Budapest | 2006 |
| 2 | Nagy | Béla | Pécs | 2005 |
| 3 | Tóth | Kata | Budapest | 2007 |
| 5 | Varga | Lili | Pécs | 2006 |

---

# NOT

Tagadás.

```sql
SELECT *
FROM sportolo
WHERE NOT varos = 'Budapest';
```

---

## Végeredmény

| id | vezeteknev | keresztnev | varos | szuletesi_ev |
|---:|---|---|---|---:|
| 2 | Nagy | Béla | Pécs | 2005 |
| 4 | Szabó | Márk | Győr | 2004 |
| 5 | Varga | Lili | Pécs | 2006 |

---

# 7. ORDER BY

Rendezésre szolgál.

---

## Növekvő sorrend

```sql
SELECT *
FROM sportolo
ORDER BY vezeteknev ASC;
```

---

## Végeredmény

| vezeteknev | keresztnev |
|---|---|
| Kovács | Anna |
| Nagy | Béla |
| Szabó | Márk |
| Tóth | Kata |
| Varga | Lili |

---

# DESC

Csökkenő sorrend.

```sql
SELECT *
FROM jelentkezes
ORDER BY pontszam DESC;
```

---

## Végeredmény

| sportolo_id | pontszam |
|---:|---:|
| 3 | 20 |
| 3 | 19 |
| 1 | 18 |
| 1 | 17 |
| 5 | 15 |
| 2 | 12 |
| 4 | 9 |

---

# 8. INSERT INTO

Új rekord beszúrása.

---

## Új sportoló hozzáadása

```sql
INSERT INTO sportolo
VALUES (6, 'Kiss', 'Dávid', 'Szeged', 2005);
```

---

## Végeredmény

| id | vezeteknev | keresztnev | varos | szuletesi_ev |
|---:|---|---|---|---:|
| 6 | Kiss | Dávid | Szeged | 2005 |

---

# 9. UPDATE

Meglévő rekord módosítása.

---

## Város módosítása

```sql
UPDATE sportolo
SET varos = 'Debrecen'
WHERE id = 6;
```

---

## Végeredmény

| id | vezeteknev | keresztnev | varos |
|---:|---|---|---|
| 6 | Kiss | Dávid | Debrecen |

---

# Nagyon fontos

WHERE nélkül az UPDATE minden rekordot módosítana.

---

# 10. DELETE

Rekord törlése.

---

## Sportoló törlése

```sql
DELETE FROM sportolo
WHERE id = 6;
```

---

## Végeredmény

A 6-os azonosítójú rekord eltűnik a táblából.

---

# Nagyon fontos

WHERE nélkül minden rekord törlődne.

---

# 11. MIN és MAX

---

# MAX

Legnagyobb érték.

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

# MIN

Legkisebb érték.

```sql
SELECT MIN(pontszam)
FROM jelentkezes;
```

---

## Végeredmény

| MIN(pontszam) |
|---:|
| 9 |

---

# 12. COUNT

Sorok számolása.

```sql
SELECT COUNT(*)
FROM sportolo;
```

---

## Végeredmény

| COUNT(*) |
|---:|
| 5 |

---

# 13. SUM

Összegzés.

```sql
SELECT SUM(pontszam)
FROM jelentkezes;
```

---

## Végeredmény

| SUM(pontszam) |
|---:|
| 110 |

---

# 14. AVG

Átlag számítása.

```sql
SELECT AVG(pontszam)
FROM jelentkezes;
```

---

## Végeredmény

| AVG(pontszam) |
|---:|
| 15.7143 |

---

# Kerekítés ROUND segítségével

```sql
SELECT ROUND(AVG(pontszam), 2)
FROM jelentkezes;
```

---

## Végeredmény

| ROUND(AVG(pontszam), 2) |
|---:|
| 15.71 |

---

# 15. LIKE

Szövegminták keresése.

---

# K betűvel kezdődő nevek

```sql
SELECT *
FROM sportolo
WHERE vezeteknev LIKE 'K%';
```

---

## Végeredmény

| vezeteknev | keresztnev |
|---|---|
| Kovács | Anna |

---

# a betűt tartalmazó keresztnevek

```sql
SELECT *
FROM sportolo
WHERE keresztnev LIKE '%a%';
```

---

## Végeredmény

| keresztnev |
|---|
| Anna |
| Kata |
| Márk |

---

# Folytasd pontosan így a többi fejezettel is...
