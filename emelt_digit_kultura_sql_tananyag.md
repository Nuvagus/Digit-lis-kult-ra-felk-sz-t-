# Emelt szintű digitális kultúra érettségi – Adatbázis-kezelés SQL-lel

**Cél:** Ez a tananyag kifejezetten a magyar emelt szintű digitális kultúra érettségi adatbázis-kezelés részére készül. Nem csak parancsokat sorol fel, hanem példákon keresztül tanítja meg, hogyan kell gondolkodni SQL-ben, hogyan kell értelmezni a feladatokat, és hogyan lehet részpontokat szerezni akkor is, ha nem tudsz mindent tökéletesen.

A példák MySQL/MariaDB szemléletben készültek, mert a digitális kultúra érettségin a hivatalosan elérhető ingyenes szoftverkörnyezetek között az XAMPP/MariaDB/phpMyAdmin jellegű adatbázis-kezelés szerepel. A legtöbb példa azonban általános SQL-logikát tanít, tehát más környezetben is érthető.

---

## 0. Hogyan gondolkodj adatbázis-feladatnál?

Az adatbázis-kezeléses érettségi feladat nem azzal kezdődik, hogy azonnal írsz egy SQL-lekérdezést. Először meg kell érteni:

1. **Milyen táblák vannak?**
2. **Milyen mezők vannak a táblákban?**
3. **Mi az elsődleges kulcs?**
4. **Hol vannak az idegen kulcsok?**
5. **Melyik tábla melyikkel kapcsolódik?**
6. **A kérdés sorokra, csoportokra vagy összesítésre vonatkozik?**
7. **Kell-e rendezés?**
8. **Kell-e ismétlődések eltüntetése?**
9. **Kell-e szöveget összefűzni?**
10. **Kell-e dátummal vagy számított értékkel dolgozni?**

Az érettségin sok pontot lehet veszíteni azzal, hogy a diák rögtön beírja a `SELECT`-et, de nem gondolja végig a táblakapcsolatokat.

---

## 1. Alapfogalmak

### Adatbázis

Az adatbázis strukturált adattárolásra szolgál. Például egy könyvtári adatbázisban külön táblában tárolhatjuk a könyveket, a szerzőket és a kölcsönzéseket.

### Tábla

A tábla sorokból és oszlopokból áll.

Példa: `diak`

| id | nev | osztaly | szuletesi_ev |
|---:|---|---|---:|
| 1 | Kovács Anna | 12.A | 2007 |
| 2 | Nagy Béla | 12.B | 2006 |
| 3 | Tóth Kata | 12.A | 2007 |

### Rekord

A rekord egy sor a táblában.

Példa: `1, Kovács Anna, 12.A, 2007`

### Mező

A mező egy oszlop.

Példa: `nev`, `osztaly`, `szuletesi_ev`

### Elsődleges kulcs

Az elsődleges kulcs egy rekordot egyértelműen azonosít.

Példa:

```sql
id INT PRIMARY KEY
```

### Idegen kulcs

Az idegen kulcs egy másik tábla elsődleges kulcsára hivatkozik.

Példa:

```text
kolcsonzes.olvaso_id → olvaso.id
```

---

## 2. Mintaadatbázis a tananyaghoz

A következő példákhoz egy sporteseményes adatbázist használunk, mert egyszerre egyszerű, de elég gazdag ahhoz, hogy minden fontos SQL-típust gyakoroljunk.

### Tábla: `sportolo`

| id | vezeteknev | keresztnev | varos | szuletesi_ev |
|---:|---|---|---|---:|
| 1 | Kovács | Anna | Budapest | 2006 |
| 2 | Nagy | Béla | Pécs | 2005 |
| 3 | Tóth | Kata | Budapest | 2007 |
| 4 | Szabó | Márk | Győr | 2004 |
| 5 | Varga | Lili | Pécs | 2006 |

### Tábla: `sportag`

| id | nev |
|---:|---|
| 1 | kosárlabda |
| 2 | kézilabda |
| 3 | futball |
| 4 | röplabda |

### Tábla: `esemeny`

| id | nev | sportag_id | helyszin | datum | max_letszam |
|---:|---|---:|---|---|---:|
| 1 | Tavaszi kupa | 1 | Budapest Aréna | 2026-03-12 | 20 |
| 2 | Városi bajnokság | 2 | Pécsi Sportcsarnok | 2026-04-03 | 16 |
| 3 | Nyári fociest | 3 | Győri pálya | 2026-06-20 | 22 |
| 4 | Röplabda nap | 4 | Budapest Aréna | 2026-05-15 | 18 |

### Tábla: `jelentkezes`

| id | sportolo_id | esemeny_id | pontszam | fizetett |
|---:|---:|---:|---:|---|
| 1 | 1 | 1 | 18 | igen |
| 2 | 2 | 1 | 12 | nem |
| 3 | 3 | 2 | 20 | igen |
| 4 | 4 | 3 | 9 | igen |
| 5 | 5 | 2 | 15 | nem |
| 6 | 1 | 4 | 17 | igen |
| 7 | 3 | 1 | 19 | igen |

---

## 3. A SELECT alapjai

### Mire számíthatsz az érettségin?

A legegyszerűbb feladatok gyakran így kezdődnek:

- listázd ki,
- jelenítsd meg,
- add meg,
- írd ki,
- rendezd,
- szűrd.

Ilyenkor szinte biztosan `SELECT` lekérdezést kell írni.

### Alapforma

```sql
SELECT oszlop1, oszlop2
FROM tabla;
```

### Példa 1: sportolók nevei

**Feladat:** Listázd ki a sportolók vezetéknevét és keresztnevét!

```sql
SELECT vezeteknev, keresztnev
FROM sportolo;
```

**Magyarázat:**

- `SELECT vezeteknev, keresztnev`: ezeket az oszlopokat kérem.
- `FROM sportolo`: ebből a táblából.

### Példa 2: minden adat lekérdezése

```sql
SELECT *
FROM sportolo;
```

A `*` minden oszlopot jelent.

**Érettségi tipp:** Ha a feladat konkrét oszlopokat kér, ne használj `*`-ot. A javításnál általában pontosan azt várják, amit a feladat kér.

---

## 4. Oszlopok átnevezése aliasokkal

Az alias olvashatóbbá teszi az eredményt.

```sql
SELECT vezeteknev AS 'Vezetéknév',
       keresztnev AS 'Keresztnév'
FROM sportolo;
```

### Mikor hasznos?

- ha számított mezőt hozol létre,
- ha összefűzött nevet készítesz,
- ha aggregáló függvényt használsz,
- ha a végeredmény oszlopcímeit is nézik.

Példa:

```sql
SELECT COUNT(*) AS 'Jelentkezések száma'
FROM jelentkezes;
```

---

## 5. DISTINCT – ismétlődések kiszűrése

### Mire jó?

A `DISTINCT` eltünteti az ismétlődő értékeket.

### Példa

**Feladat:** Add meg, mely városokból vannak sportolók!

```sql
SELECT DISTINCT varos
FROM sportolo;
```

### Eredmény

| varos |
|---|
| Budapest |
| Pécs |
| Győr |

### Mi történne DISTINCT nélkül?

```sql
SELECT varos
FROM sportolo;
```

Eredmény:

| varos |
|---|
| Budapest |
| Pécs |
| Budapest |
| Győr |
| Pécs |

### Tipikus érettségi megfogalmazás

Ha a feladat így fogalmaz:

- „mely városokból”
- „milyen kategóriák vannak”
- „sorolja fel az eltérő értékeket”
- „ismétlődés nélkül”

akkor nagyon gyakran `DISTINCT` kell.

### Tipikus hiba

```sql
SELECT DISTINCT varos, vezeteknev
FROM sportolo;
```

Ez már a `varos + vezeteknev` kombinációkat teszi egyedivé, nem csak a városokat. Ha csak városokra kíváncsi a feladat, csak a `varos` legyen a SELECT-ben.

---

## 6. CONCAT – szövegek összefűzése

### Mire jó?

A `CONCAT` több szöveget egyetlen szöveggé fűz össze.

### Példa: teljes név

```sql
SELECT CONCAT(vezeteknev, ' ', keresztnev) AS teljes_nev
FROM sportolo;
```

### Eredmény

| teljes_nev |
|---|
| Kovács Anna |
| Nagy Béla |
| Tóth Kata |
| Szabó Márk |
| Varga Lili |

### Magyarázat

```sql
CONCAT(vezeteknev, ' ', keresztnev)
```

Ez három részt fűz össze:

1. vezetéknév,
2. szóköz,
3. keresztnév.

### Érettségi típusfeladat

**Feladat:** Jelenítsd meg a sportolók teljes nevét és városát ilyen formában:

```text
Kovács Anna - Budapest
```

Megoldás:

```sql
SELECT CONCAT(vezeteknev, ' ', keresztnev, ' - ', varos) AS adat
FROM sportolo;
```

### Tipikus hiba

A szóköz elmarad:

```sql
SELECT CONCAT(vezeteknev, keresztnev)
FROM sportolo;
```

Eredmény:

```text
KovácsAnna
```

Ez formailag hibás lehet.

---

## 7. Szűrés WHERE-rel

### Alapforma

```sql
SELECT oszlopok
FROM tabla
WHERE feltetel;
```

### Példa 1: budapesti sportolók

```sql
SELECT vezeteknev, keresztnev
FROM sportolo
WHERE varos = 'Budapest';
```

### Példa 2: 2006 után születettek

```sql
SELECT vezeteknev, keresztnev, szuletesi_ev
FROM sportolo
WHERE szuletesi_ev > 2006;
```

### Operátorok

| Operátor | Jelentés |
|---|---|
| `=` | egyenlő |
| `<>` | nem egyenlő |
| `>` | nagyobb |
| `<` | kisebb |
| `>=` | nagyobb vagy egyenlő |
| `<=` | kisebb vagy egyenlő |

---

## 8. Több feltétel: AND, OR, NOT

### AND

Mindkét feltételnek teljesülnie kell.

**Feladat:** Budapesti és 2006-ban született sportolók.

```sql
SELECT vezeteknev, keresztnev
FROM sportolo
WHERE varos = 'Budapest'
  AND szuletesi_ev = 2006;
```

### OR

Elég, ha az egyik feltétel teljesül.

**Feladat:** Budapesti vagy pécsi sportolók.

```sql
SELECT vezeteknev, keresztnev, varos
FROM sportolo
WHERE varos = 'Budapest'
   OR varos = 'Pécs';
```

### NOT

Tagadás.

```sql
SELECT vezeteknev, keresztnev, varos
FROM sportolo
WHERE NOT varos = 'Budapest';
```

Ez ugyanaz, mint:

```sql
SELECT vezeteknev, keresztnev, varos
FROM sportolo
WHERE varos <> 'Budapest';
```

### Zárójelezés

Nagyon fontos, mert az `AND` és `OR` keverése félrevezető lehet.

```sql
SELECT vezeteknev, keresztnev, varos, szuletesi_ev
FROM sportolo
WHERE (varos = 'Budapest' OR varos = 'Pécs')
  AND szuletesi_ev = 2006;
```

Ez azt jelenti:

- Budapest vagy Pécs,
- és közben 2006-os születésű.

---

## 9. IN – több lehetséges érték

Az `IN` rövidebb megoldás sok `OR` helyett.

### Példa

```sql
SELECT vezeteknev, keresztnev, varos
FROM sportolo
WHERE varos IN ('Budapest', 'Pécs');
```

Ez ugyanaz, mint:

```sql
WHERE varos = 'Budapest' OR varos = 'Pécs'
```

### Mikor hasznos?

Ha a feladat több konkrét értéket sorol fel.

---

## 10. BETWEEN – tartományos szűrés

### Példa

**Feladat:** Add meg a 2005 és 2007 között született sportolókat!

```sql
SELECT vezeteknev, keresztnev, szuletesi_ev
FROM sportolo
WHERE szuletesi_ev BETWEEN 2005 AND 2007;
```

### Fontos

A `BETWEEN` mindkét szélső értéket tartalmazza.

Tehát ez tartalmazza:

- 2005,
- 2006,
- 2007.

---

## 11. LIKE – szövegminták keresése

### `%` jel

A `%` tetszőleges hosszúságú szöveget jelent.

### Példa 1: K betűvel kezdődő vezetéknevek

```sql
SELECT vezeteknev, keresztnev
FROM sportolo
WHERE vezeteknev LIKE 'K%';
```

### Példa 2: a betűt tartalmazó nevek

```sql
SELECT vezeteknev, keresztnev
FROM sportolo
WHERE keresztnev LIKE '%a%';
```

### Példa 3: a betűre végződő nevek

```sql
SELECT vezeteknev, keresztnev
FROM sportolo
WHERE keresztnev LIKE '%a';
```

### `_` jel

Az `_` pontosan egy karaktert jelent.

```sql
SELECT vezeteknev, keresztnev
FROM sportolo
WHERE keresztnev LIKE 'A__a';
```

Ez olyan négybetűs nevet keres, amely:

- A-val kezdődik,
- a-val végződik,
- közte két karakter van.

### Tipikus hiba

```sql
WHERE nev = 'K%'
```

Ez hibás logika, mert az `=` pontos egyezést keres. Mintához `LIKE` kell.

---

## 12. Rendezés ORDER BY-jal

### Példa 1: név szerint növekvő sorrend

```sql
SELECT vezeteknev, keresztnev
FROM sportolo
ORDER BY vezeteknev ASC, keresztnev ASC;
```

Az `ASC` növekvő sorrendet jelent. Alapértelmezetten is növekvő, de érettségin nyugodtan kiírhatod.

### Példa 2: pontszám szerint csökkenő sorrend

```sql
SELECT sportolo_id, pontszam
FROM jelentkezes
ORDER BY pontszam DESC;
```

### Tipikus hiba

A feladat „legmagasabbtól legalacsonyabbig” sorrendet kér, de a diák elfelejti a `DESC`-et.

---

## 13. LIMIT – első néhány rekord

MySQL/MariaDB környezetben a `LIMIT` használható.

### Példa: legjobb 3 pontszám

```sql
SELECT sportolo_id, pontszam
FROM jelentkezes
ORDER BY pontszam DESC
LIMIT 3;
```

### Fontos

A `LIMIT` önmagában nem elég. Ha „legnagyobb”, „legkisebb”, „első 3” szerepel a feladatban, akkor általában kell `ORDER BY` is.

---

## 14. Aggregáló függvények

Az aggregáló függvények több rekordból számolnak egy értéket.

### Legfontosabbak

| Függvény | Jelentés |
|---|---|
| `COUNT()` | darabszám |
| `SUM()` | összeg |
| `AVG()` | átlag |
| `MIN()` | minimum |
| `MAX()` | maximum |

---

## 15. COUNT – darabszám

### Összes jelentkezés száma

```sql
SELECT COUNT(*) AS jelentkezesek_szama
FROM jelentkezes;
```

### Fizetett jelentkezések száma

```sql
SELECT COUNT(*) AS fizetett_jelentkezesek
FROM jelentkezes
WHERE fizetett = 'igen';
```

### COUNT(oszlop) vs COUNT(*)

- `COUNT(*)`: minden sort megszámol.
- `COUNT(oszlop)`: csak azokat, ahol az adott oszlop nem NULL.

Érettségin legtöbbször `COUNT(*)` biztonságosabb, ha sorok számát kell meghatározni.

---

## 16. SUM – összegzés

### Összes pontszám

```sql
SELECT SUM(pontszam) AS osszpontszam
FROM jelentkezes;
```

### Csak fizetett jelentkezések pontszáma

```sql
SELECT SUM(pontszam) AS fizetett_osszpontszam
FROM jelentkezes
WHERE fizetett = 'igen';
```

---

## 17. AVG – átlag

### Átlagpontszám

```sql
SELECT AVG(pontszam) AS atlagpontszam
FROM jelentkezes;
```

### Kerekítés ROUND-dal

```sql
SELECT ROUND(AVG(pontszam), 2) AS atlagpontszam
FROM jelentkezes;
```

Ez két tizedesjegyre kerekít.

---

## 18. MIN és MAX

### Legkisebb pontszám

```sql
SELECT MIN(pontszam) AS legkisebb
FROM jelentkezes;
```

### Legnagyobb pontszám

```sql
SELECT MAX(pontszam) AS legnagyobb
FROM jelentkezes;
```

### Fontos

Ez csak az értéket adja vissza, nem feltétlenül a hozzá tartozó személyt.

Ha a legnagyobb pontszámot elérő személy nevét is kéred, akkor más megoldás kell, például rendezés + LIMIT vagy al-lekérdezés.

---

## 19. Legnagyobb értékhez tartozó rekord

### Egyszerű MySQL-megoldás

**Feladat:** Ki érte el a legmagasabb pontszámot?

```sql
SELECT s.vezeteknev, s.keresztnev, j.pontszam
FROM sportolo s
JOIN jelentkezes j ON s.id = j.sportolo_id
ORDER BY j.pontszam DESC
LIMIT 1;
```

### Ha több azonos legnagyobb pontszám is lehet

```sql
SELECT s.vezeteknev, s.keresztnev, j.pontszam
FROM sportolo s
JOIN jelentkezes j ON s.id = j.sportolo_id
WHERE j.pontszam = (
    SELECT MAX(pontszam)
    FROM jelentkezes
);
```

### Magyarázat

A belső lekérdezés:

```sql
SELECT MAX(pontszam)
FROM jelentkezes
```

megkeresi a legnagyobb pontszámot.

A külső lekérdezés pedig megkeresi azokat, akik pontosan ezt az értéket érték el.

---

## 20. GROUP BY – csoportosítás

A `GROUP BY` az SQL egyik legfontosabb része emelt szinten.

### Mikor kell?

Ha a kérdésben ilyenek vannak:

- városonként,
- sportáganként,
- eseményenként,
- kategóriánként,
- személyenként,
- évente,
- havonta,
- csoportonként.

### Példa: városonként hány sportoló van?

```sql
SELECT varos, COUNT(*) AS letszam
FROM sportolo
GROUP BY varos;
```

### Eredmény

| varos | letszam |
|---|---:|
| Budapest | 2 |
| Pécs | 2 |
| Győr | 1 |

### Nagyon fontos szabály

Ha a `SELECT` részben van egy nem aggregált oszlop, akkor annak általában szerepelnie kell a `GROUP BY` részben is.

Helyes:

```sql
SELECT varos, COUNT(*)
FROM sportolo
GROUP BY varos;
```

Hibás logika:

```sql
SELECT varos, vezeteknev, COUNT(*)
FROM sportolo
GROUP BY varos;
```

Itt a `vezeteknev` nem egyértelmű, mert egy városban több vezetéknév is lehet.

---

## 21. HAVING – csoportok szűrése

### WHERE vs HAVING

| WHERE | HAVING |
|---|---|
| sorokat szűr | csoportokat szűr |
| GROUP BY előtt történik | GROUP BY után történik |
| sima mezőkre használjuk | aggregált eredményekre használjuk |

### Példa: csak azok a városok, ahonnan legalább 2 sportoló van

```sql
SELECT varos, COUNT(*) AS letszam
FROM sportolo
GROUP BY varos
HAVING COUNT(*) >= 2;
```

### Tipikus hiba

Hibás:

```sql
SELECT varos, COUNT(*) AS letszam
FROM sportolo
WHERE COUNT(*) >= 2
GROUP BY varos;
```

A `COUNT(*)` csoportosítás után keletkezik, ezért `WHERE`-ben nem használható.

---

## 22. GROUP BY + ORDER BY

### Feladat

Add meg városonként a sportolók számát, legtöbbtől legkevesebbig!

```sql
SELECT varos, COUNT(*) AS letszam
FROM sportolo
GROUP BY varos
ORDER BY letszam DESC;
```

### Fontos

A MySQL/MariaDB engedi, hogy az aliasra rendezz:

```sql
ORDER BY letszam DESC
```

---

## 23. JOIN – táblák összekapcsolása

A JOIN az adatbázis-kezelés lelke.

### Miért kell?

Mert az adatok több táblában vannak szétszedve.

Példa:

- A sportoló neve a `sportolo` táblában van.
- A pontszáma a `jelentkezes` táblában van.
- Az esemény neve az `esemeny` táblában van.

Ha ezeket együtt akarod megjeleníteni, JOIN kell.

---

## 24. INNER JOIN

Az `INNER JOIN` csak azokat a rekordokat adja vissza, ahol mindkét táblában van kapcsolódó adat.

### Példa: sportolók és pontszámaik

```sql
SELECT s.vezeteknev, s.keresztnev, j.pontszam
FROM sportolo s
JOIN jelentkezes j ON s.id = j.sportolo_id;
```

### Magyarázat

- `sportolo s`: a sportolo tábla rövid neve `s`.
- `jelentkezes j`: a jelentkezes tábla rövid neve `j`.
- `ON s.id = j.sportolo_id`: ez a kapcsolat.

---

## 25. Több táblás JOIN

### Feladat

Listázd ki, hogy melyik sportoló melyik eseményre jelentkezett!

```sql
SELECT s.vezeteknev, s.keresztnev, e.nev AS esemeny_nev
FROM sportolo s
JOIN jelentkezes j ON s.id = j.sportolo_id
JOIN esemeny e ON e.id = j.esemeny_id;
```

### Feladat

Listázd ki, hogy melyik sportoló milyen sportág eseményére jelentkezett!

```sql
SELECT s.vezeteknev,
       s.keresztnev,
       e.nev AS esemeny,
       sp.nev AS sportag
FROM sportolo s
JOIN jelentkezes j ON s.id = j.sportolo_id
JOIN esemeny e ON e.id = j.esemeny_id
JOIN sportag sp ON sp.id = e.sportag_id;
```

### Érettségi gondolkodás

A kapcsolati lánc:

```text
sportolo → jelentkezes → esemeny → sportag
```

Ha a kért adat két táblával arrébb van, több JOIN kell.

---

## 26. LEFT JOIN

A `LEFT JOIN` akkor is megjeleníti a bal oldali tábla rekordjait, ha nincs hozzájuk kapcsolódó adat a jobb oldali táblában.

### Példa

**Feladat:** Listázd ki az összes sportolót, és ha van jelentkezése, annak azonosítóját!

```sql
SELECT s.vezeteknev, s.keresztnev, j.id AS jelentkezes_id
FROM sportolo s
LEFT JOIN jelentkezes j ON s.id = j.sportolo_id;
```

### Mikor kell LEFT JOIN?

Ha a feladat így fogalmaz:

- azok is jelenjenek meg, akiknek nincs...
- minden sportolót listázz,
- akkor is, ha nem jelentkezett,
- hiányzó kapcsolatok keresése.

### Akik nem jelentkeztek semmire

```sql
SELECT s.vezeteknev, s.keresztnev
FROM sportolo s
LEFT JOIN jelentkezes j ON s.id = j.sportolo_id
WHERE j.id IS NULL;
```

### Magyarázat

A `LEFT JOIN` miatt minden sportoló bekerül. Ahol nincs jelentkezés, ott a `j.id` értéke `NULL`.

---

## 27. NULL kezelése

A `NULL` azt jelenti: nincs adat / ismeretlen érték.

### Hibás

```sql
WHERE j.id = NULL
```

### Helyes

```sql
WHERE j.id IS NULL
```

### Nem NULL

```sql
WHERE j.id IS NOT NULL
```

---

## 28. Dátumfüggvények

Az érettségin gyakran kell dátumokkal dolgozni: év, hónap, nap, adott időszak.

### YEAR()

```sql
SELECT nev, datum
FROM esemeny
WHERE YEAR(datum) = 2026;
```

### MONTH()

```sql
SELECT nev, datum
FROM esemeny
WHERE MONTH(datum) = 5;
```

### DAY()

```sql
SELECT nev, datum
FROM esemeny
WHERE DAY(datum) = 15;
```

### Dátumtartomány

```sql
SELECT nev, datum
FROM esemeny
WHERE datum BETWEEN '2026-04-01' AND '2026-06-30';
```

### Rendezés dátum szerint

```sql
SELECT nev, datum
FROM esemeny
ORDER BY datum ASC;
```

### Tipikus hiba

Dátumot idézőjel nélkül írnak:

```sql
WHERE datum = 2026-05-15
```

Ez hibás lehet, mert az SQL számként vagy kifejezésként értelmezheti. Helyesen:

```sql
WHERE datum = '2026-05-15'
```

---

## 29. Szövegfüggvények

### LENGTH()

Karakterlánc hosszát adja meg bájtban. Magyar ékezeteknél környezettől függően eltérhet.

```sql
SELECT vezeteknev, LENGTH(vezeteknev) AS hossz
FROM sportolo;
```

### CHAR_LENGTH()

Karakterek számát adja meg. Ékezetes magyar szövegeknél általában jobb.

```sql
SELECT vezeteknev, CHAR_LENGTH(vezeteknev) AS karakterek
FROM sportolo;
```

### UPPER()

Nagybetűssé alakít.

```sql
SELECT UPPER(vezeteknev) AS nagybetus
FROM sportolo;
```

### LOWER()

Kisbetűssé alakít.

```sql
SELECT LOWER(vezeteknev) AS kisbetus
FROM sportolo;
```

### SUBSTRING()

Részletet vág ki szövegből.

```sql
SELECT vezeteknev, SUBSTRING(vezeteknev, 1, 1) AS elso_betu
FROM sportolo;
```

### CONCAT() ismétlés

```sql
SELECT CONCAT(UPPER(vezeteknev), ' ', keresztnev) AS nev
FROM sportolo;
```

---

## 30. Matematikai és számfüggvények

### ROUND()

Kerekítés.

```sql
SELECT ROUND(AVG(pontszam), 1) AS atlag
FROM jelentkezes;
```

### FLOOR()

Lefelé kerekítés.

```sql
SELECT FLOOR(AVG(pontszam)) AS atlag_lefele
FROM jelentkezes;
```

### CEIL()

Felfelé kerekítés.

```sql
SELECT CEIL(AVG(pontszam)) AS atlag_felfele
FROM jelentkezes;
```

### Számított mezők

```sql
SELECT pontszam,
       pontszam * 2 AS dupla_pont
FROM jelentkezes;
```

---

## 31. CASE WHEN – feltételes logika SQL-ben

A `CASE WHEN` olyan, mint Excelben a `HA`.

### Példa

**Feladat:** A pontszám alapján minősítés:

- 18 ponttól: kiváló
- 12 ponttól: megfelelő
- különben: gyenge

```sql
SELECT sportolo_id,
       pontszam,
       CASE
           WHEN pontszam >= 18 THEN 'kiváló'
           WHEN pontszam >= 12 THEN 'megfelelő'
           ELSE 'gyenge'
       END AS minosites
FROM jelentkezes;
```

### Fontos

A feltételek sorrendje számít. Először a legerősebb feltételt érdemes írni.

---

## 32. Al-lekérdezések

Az al-lekérdezés egy lekérdezés egy másik lekérdezésen belül.

### Példa 1: átlag feletti pontszámok

```sql
SELECT sportolo_id, pontszam
FROM jelentkezes
WHERE pontszam > (
    SELECT AVG(pontszam)
    FROM jelentkezes
);
```

### Magyarázat

A belső lekérdezés kiszámolja az átlagot. A külső lekérdezés megkeresi az átlagnál nagyobb pontszámokat.

### Példa 2: legnagyobb pontszámot elérők

```sql
SELECT s.vezeteknev, s.keresztnev, j.pontszam
FROM sportolo s
JOIN jelentkezes j ON s.id = j.sportolo_id
WHERE j.pontszam = (
    SELECT MAX(pontszam)
    FROM jelentkezes
);
```

---

## 33. NOT IN és IN al-lekérdezéssel

### Feladat

Add meg azokat a sportolókat, akik jelentkeztek legalább egy eseményre.

```sql
SELECT vezeteknev, keresztnev
FROM sportolo
WHERE id IN (
    SELECT sportolo_id
    FROM jelentkezes
);
```

### Akik nem jelentkeztek eseményre

```sql
SELECT vezeteknev, keresztnev
FROM sportolo
WHERE id NOT IN (
    SELECT sportolo_id
    FROM jelentkezes
);
```

### Érettségi tipp

Ugyanez megoldható `LEFT JOIN` + `IS NULL` módszerrel is. A javítás általában a helyes eredményt és logikát értékeli.

---

## 34. EXISTS

Az `EXISTS` azt vizsgálja, hogy létezik-e legalább egy kapcsolódó rekord.

### Példa

```sql
SELECT s.vezeteknev, s.keresztnev
FROM sportolo s
WHERE EXISTS (
    SELECT 1
    FROM jelentkezes j
    WHERE j.sportolo_id = s.id
);
```

Ez azokat adja vissza, akiknek van jelentkezésük.

### Mikor hasznos?

Komplexebb feladatoknál, ahol a létezés ténye fontosabb, mint maga az érték.

---

## 35. Többszintű csoportosítás

### Feladat

Add meg eseményenként, hány jelentkezés történt!

```sql
SELECT e.nev AS esemeny, COUNT(*) AS jelentkezesek
FROM esemeny e
JOIN jelentkezes j ON e.id = j.esemeny_id
GROUP BY e.nev;
```

### Feladat

Add meg sportáganként a jelentkezések számát!

```sql
SELECT sp.nev AS sportag, COUNT(*) AS jelentkezesek
FROM sportag sp
JOIN esemeny e ON sp.id = e.sportag_id
JOIN jelentkezes j ON e.id = j.esemeny_id
GROUP BY sp.nev;
```

### Feladat

Csak azok a sportágak jelenjenek meg, ahol legalább 2 jelentkezés van!

```sql
SELECT sp.nev AS sportag, COUNT(*) AS jelentkezesek
FROM sportag sp
JOIN esemeny e ON sp.id = e.sportag_id
JOIN jelentkezes j ON e.id = j.esemeny_id
GROUP BY sp.nev
HAVING COUNT(*) >= 2;
```

---

## 36. DISTINCT + COUNT

### Feladat

Hány különböző városból vannak sportolók?

```sql
SELECT COUNT(DISTINCT varos) AS varosok_szama
FROM sportolo;
```

### Feladat

Hány különböző sportoló jelentkezett eseményre?

```sql
SELECT COUNT(DISTINCT sportolo_id) AS sportolok_szama
FROM jelentkezes;
```

### Fontos különbség

```sql
COUNT(sportolo_id)
```

minden jelentkezést számol.

```sql
COUNT(DISTINCT sportolo_id)
```

minden sportolót egyszer számol.

Ez érettségin nagyon gyakori pontvesztési hely.

---

## 37. CONCAT + GROUP BY példa

### Feladat

Add meg sportolónként a jelentkezéseik számát teljes névvel!

```sql
SELECT CONCAT(s.vezeteknev, ' ', s.keresztnev) AS teljes_nev,
       COUNT(j.id) AS jelentkezesek_szama
FROM sportolo s
JOIN jelentkezes j ON s.id = j.sportolo_id
GROUP BY s.id, s.vezeteknev, s.keresztnev;
```

### Miért szerepel több mező a GROUP BY-ban?

Mert a teljes név több mezőből áll. A legbiztonságosabb, ha a csoportosításban szerepel:

- az azonosító,
- a vezetéknév,
- a keresztnév.

---

## 38. Rendezés összesített érték alapján

### Feladat

Add meg sportolónként az átlagpontszámot, legmagasabb átlagtól lefelé!

```sql
SELECT CONCAT(s.vezeteknev, ' ', s.keresztnev) AS teljes_nev,
       ROUND(AVG(j.pontszam), 2) AS atlagpont
FROM sportolo s
JOIN jelentkezes j ON s.id = j.sportolo_id
GROUP BY s.id, s.vezeteknev, s.keresztnev
ORDER BY atlagpont DESC;
```

---

## 39. Adatbázis létrehozása – alapszint

Érettségin jellemzően meglévő adatbázisból kell dolgozni, de fontos érteni a táblák szerkezetét.

### CREATE TABLE példa

```sql
CREATE TABLE sportolo (
    id INT PRIMARY KEY,
    vezeteknev VARCHAR(50),
    keresztnev VARCHAR(50),
    varos VARCHAR(50),
    szuletesi_ev INT
);
```

### INSERT példa

```sql
INSERT INTO sportolo (id, vezeteknev, keresztnev, varos, szuletesi_ev)
VALUES
(1, 'Kovács', 'Anna', 'Budapest', 2006),
(2, 'Nagy', 'Béla', 'Pécs', 2005);
```

---

## 40. Importált adatok ellenőrzése

Ha forrásfájlból importálsz, mindig ellenőrizd:

1. jók-e az oszlopnevek,
2. jó adattípus került-e be,
3. nincs-e elcsúszott oszlop,
4. az ékezetes karakterek jól jelennek-e meg,
5. a dátum mezők valóban dátumként működnek-e.

### Gyors ellenőrző lekérdezések

```sql
SELECT *
FROM sportolo
LIMIT 10;
```

```sql
SELECT COUNT(*)
FROM sportolo;
```

```sql
DESCRIBE sportolo;
```

---

## 41. Normalizálás alapjai

Az adatbázis-kezelésnél fontos megérteni, miért vannak az adatok több táblába szedve.

### Rossz tárolás

| sportolo | sportag | esemeny |
|---|---|---|
| Kovács Anna | kosárlabda | Tavaszi kupa |
| Kovács Anna | röplabda | Röplabda nap |

Itt Kovács Anna adatai többször ismétlődnek.

### Jobb tárolás

- `sportolo`
- `sportag`
- `esemeny`
- `jelentkezes`

Ez csökkenti az ismétlődést és könnyebbé teszi a módosítást.

### Kapcsolattípusok

| Kapcsolat | Példa |
|---|---|
| egy-egy | személy – igazolvány |
| egy-több | sportág – esemény |
| több-több | sportoló – esemény |

A több-több kapcsolatot kapcsolótáblával kezeljük.

Példa: `jelentkezes`

---

## 42. Tipikus érettségi feladatmegoldási stratégia SQL-hez

### 1. Olvasd el a feladatot kétszer

Keresd a kulcsszavakat:

- „hány” → `COUNT`
- „összesen” → `SUM`
- „átlag” → `AVG`
- „legnagyobb” → `MAX` vagy `ORDER BY DESC LIMIT 1`
- „legkisebb” → `MIN` vagy `ORDER BY ASC LIMIT 1`
- „különböző” → `DISTINCT`
- „városonként” → `GROUP BY varos`
- „legalább” csoportokra → `HAVING`
- „akiknek nincs” → `LEFT JOIN` + `IS NULL`

### 2. Írd fel, melyik táblák kellenek

Példa:

„Add meg a budapesti események sportágainak nevét!”

Kell:

- `esemeny`, mert ott van a helyszín.
- `sportag`, mert ott van a sportág neve.

### 3. Írd fel a kapcsolatot

```text
esemeny.sportag_id = sportag.id
```

### 4. Csak ezután írd meg az SQL-t

```sql
SELECT sp.nev
FROM esemeny e
JOIN sportag sp ON sp.id = e.sportag_id
WHERE e.helyszin LIKE '%Budapest%';
```

---

## 43. Komplett gyakorlófeladat-sor

### Adatbázis

Használd a korábbi `sportolo`, `sportag`, `esemeny`, `jelentkezes` táblákat.

---

### Feladat 1

Listázd ki a sportolók teljes nevét és városát!

#### Megoldás

```sql
SELECT CONCAT(vezeteknev, ' ', keresztnev) AS teljes_nev,
       varos
FROM sportolo;
```

---

### Feladat 2

Add meg ismétlődés nélkül, hogy mely városokból vannak sportolók!

#### Megoldás

```sql
SELECT DISTINCT varos
FROM sportolo;
```

---

### Feladat 3

Listázd ki a 2006-ban vagy később született sportolókat név szerint rendezve!

#### Megoldás

```sql
SELECT vezeteknev, keresztnev, szuletesi_ev
FROM sportolo
WHERE szuletesi_ev >= 2006
ORDER BY vezeteknev, keresztnev;
```

---

### Feladat 4

Add meg, hány jelentkezés történt összesen!

#### Megoldás

```sql
SELECT COUNT(*) AS jelentkezesek_szama
FROM jelentkezes;
```

---

### Feladat 5

Add meg, hány különböző sportoló jelentkezett eseményre!

#### Megoldás

```sql
SELECT COUNT(DISTINCT sportolo_id) AS sportolok_szama
FROM jelentkezes;
```

---

### Feladat 6

Add meg a fizetett jelentkezések átlagpontszámát két tizedesjegyre kerekítve!

#### Megoldás

```sql
SELECT ROUND(AVG(pontszam), 2) AS atlagpont
FROM jelentkezes
WHERE fizetett = 'igen';
```

---

### Feladat 7

Listázd ki a sportolók nevét és az események nevét, amelyekre jelentkeztek!

#### Megoldás

```sql
SELECT CONCAT(s.vezeteknev, ' ', s.keresztnev) AS sportolo,
       e.nev AS esemeny
FROM sportolo s
JOIN jelentkezes j ON s.id = j.sportolo_id
JOIN esemeny e ON e.id = j.esemeny_id;
```

---

### Feladat 8

Add meg eseményenként a jelentkezések számát!

#### Megoldás

```sql
SELECT e.nev AS esemeny,
       COUNT(j.id) AS jelentkezesek_szama
FROM esemeny e
JOIN jelentkezes j ON e.id = j.esemeny_id
GROUP BY e.id, e.nev;
```

---

### Feladat 9

Csak azok az események jelenjenek meg, amelyekre legalább 2 jelentkezés történt!

#### Megoldás

```sql
SELECT e.nev AS esemeny,
       COUNT(j.id) AS jelentkezesek_szama
FROM esemeny e
JOIN jelentkezes j ON e.id = j.esemeny_id
GROUP BY e.id, e.nev
HAVING COUNT(j.id) >= 2;
```

---

### Feladat 10

Add meg sportáganként a jelentkezések számát, csökkenő sorrendben!

#### Megoldás

```sql
SELECT sp.nev AS sportag,
       COUNT(j.id) AS jelentkezesek_szama
FROM sportag sp
JOIN esemeny e ON sp.id = e.sportag_id
JOIN jelentkezes j ON e.id = j.esemeny_id
GROUP BY sp.id, sp.nev
ORDER BY jelentkezesek_szama DESC;
```

---

### Feladat 11

Add meg a legmagasabb pontszámot elérő sportoló nevét!

#### Megoldás 1: LIMIT-tel

```sql
SELECT CONCAT(s.vezeteknev, ' ', s.keresztnev) AS sportolo,
       j.pontszam
FROM sportolo s
JOIN jelentkezes j ON s.id = j.sportolo_id
ORDER BY j.pontszam DESC
LIMIT 1;
```

#### Megoldás 2: al-lekérdezéssel

```sql
SELECT CONCAT(s.vezeteknev, ' ', s.keresztnev) AS sportolo,
       j.pontszam
FROM sportolo s
JOIN jelentkezes j ON s.id = j.sportolo_id
WHERE j.pontszam = (
    SELECT MAX(pontszam)
    FROM jelentkezes
);
```

A második megoldás akkor jobb, ha több azonos legmagasabb pontszám is lehet.

---

### Feladat 12

Add meg azokat a sportolókat, akiknek az átlagpontszáma legalább 17!

#### Megoldás

```sql
SELECT CONCAT(s.vezeteknev, ' ', s.keresztnev) AS sportolo,
       ROUND(AVG(j.pontszam), 2) AS atlagpont
FROM sportolo s
JOIN jelentkezes j ON s.id = j.sportolo_id
GROUP BY s.id, s.vezeteknev, s.keresztnev
HAVING AVG(j.pontszam) >= 17;
```

---

### Feladat 13

Add meg azokat az eseményeket, amelyek Budapesten vannak!

#### Megoldás

```sql
SELECT nev, helyszin, datum
FROM esemeny
WHERE helyszin LIKE '%Budapest%';
```

---

### Feladat 14

Add meg a májusi eseményeket!

#### Megoldás

```sql
SELECT nev, datum
FROM esemeny
WHERE MONTH(datum) = 5;
```

---

### Feladat 15

Add meg azokat a sportolókat, akik nem fizettek valamelyik jelentkezésüknél!

#### Megoldás

```sql
SELECT DISTINCT CONCAT(s.vezeteknev, ' ', s.keresztnev) AS sportolo
FROM sportolo s
JOIN jelentkezes j ON s.id = j.sportolo_id
WHERE j.fizetett = 'nem';
```

A `DISTINCT` azért kell, mert ha valaki több nem fizetett jelentkezéssel is rendelkezik, akkor különben többször jelenne meg.

---

## 44. Hibakeresési gyakorlatok

### Hiba 1

```sql
SELECT varos, COUNT(*)
FROM sportolo
WHERE COUNT(*) > 1
GROUP BY varos;
```

#### Mi a hiba?

A `COUNT(*)` csoportosított érték, ezért nem kerülhet a `WHERE` részbe.

#### Javítás

```sql
SELECT varos, COUNT(*) AS letszam
FROM sportolo
GROUP BY varos
HAVING COUNT(*) > 1;
```

---

### Hiba 2

```sql
SELECT vezeteknev keresztnev
FROM sportolo;
```

#### Mi a hiba?

Hiányzik a vessző. Az SQL ezt aliasnak is értelmezheti, nem két oszlopnak.

#### Javítás

```sql
SELECT vezeteknev, keresztnev
FROM sportolo;
```

---

### Hiba 3

```sql
SELECT s.vezeteknev, e.nev
FROM sportolo s
JOIN esemeny e ON s.id = e.id;
```

#### Mi a hiba?

A `sportolo` és `esemeny` között nincs közvetlen kapcsolat. A kapcsolat a `jelentkezes` táblán keresztül megy.

#### Javítás

```sql
SELECT s.vezeteknev, e.nev
FROM sportolo s
JOIN jelentkezes j ON s.id = j.sportolo_id
JOIN esemeny e ON e.id = j.esemeny_id;
```

---

### Hiba 4

```sql
SELECT DISTINCT varos, vezeteknev
FROM sportolo;
```

#### Mi lehet a gond?

Ha a feladat csak különböző városokat kér, akkor a vezetéknév miatt nem csak a városok lesznek egyediek.

#### Javítás

```sql
SELECT DISTINCT varos
FROM sportolo;
```

---

### Hiba 5

```sql
SELECT CONCAT(vezeteknev, keresztnev) AS teljes_nev
FROM sportolo;
```

#### Mi a gond?

A névben nincs szóköz.

#### Javítás

```sql
SELECT CONCAT(vezeteknev, ' ', keresztnev) AS teljes_nev
FROM sportolo;
```

---

## 45. SQL parancssorrend – amit kívülről kell tudni

A lekérdezés írási sorrendje:

```sql
SELECT
FROM
JOIN
WHERE
GROUP BY
HAVING
ORDER BY
LIMIT
```

A feldolgozás logikai sorrendje nagyjából:

1. FROM
2. JOIN
3. WHERE
4. GROUP BY
5. HAVING
6. SELECT
7. ORDER BY
8. LIMIT

Ez azért fontos, mert így érted meg, miért nem használható a `COUNT(*)` a `WHERE` részben.

---

## 46. Vizsgára szánt SQL puskázó gondolatmenet

Nem puskázásként, hanem fejben követendő ellenőrzőlistaként:

### Ha listázni kell

```sql
SELECT ...
FROM ...
WHERE ...
ORDER BY ...;
```

### Ha számolni kell

```sql
SELECT COUNT(*)
FROM ...
WHERE ...;
```

### Ha csoportonként kell számolni

```sql
SELECT csoport_mezo, COUNT(*)
FROM ...
GROUP BY csoport_mezo;
```

### Ha csoportonként kell szűrni

```sql
SELECT csoport_mezo, COUNT(*)
FROM ...
GROUP BY csoport_mezo
HAVING COUNT(*) > ...;
```

### Ha több tábla kell

```sql
SELECT ...
FROM tabla1 t1
JOIN tabla2 t2 ON t1.kulcs = t2.idegen_kulcs;
```

### Ha teljes név kell

```sql
CONCAT(vezeteknev, ' ', keresztnev)
```

### Ha ismétlődés nélküli lista kell

```sql
SELECT DISTINCT ...
```

### Ha a legnagyobb/legkisebb rekord kell

```sql
ORDER BY mezo DESC
LIMIT 1
```

vagy:

```sql
WHERE mezo = (SELECT MAX(mezo) FROM tabla)
```

---

## 47. Pontszerzési stratégia érettségin

### 1. Ne hagyj üres SQL-feladatot

Még egy részleges lekérdezés is érhet pontot.

Példa:

```sql
SELECT nev
FROM esemeny;
```

Ha a teljes JOIN nem sikerül, ez is mutatja, hogy érted, melyik táblából kell indulni.

### 2. Használj aliasokat

```sql
FROM sportolo s
JOIN jelentkezes j ON s.id = j.sportolo_id
```

Rövidebb, átláthatóbb, kevesebb hibalehetőség.

### 3. Először működjön, utána szépítsd

Ne akarj elsőre tökéletes lekérdezést. Először legyen helyes eredmény, utána alias, kerekítés, rendezés.

### 4. Ellenőrizd részletekben

Komplex lekérdezésnél először csak ezt futtasd:

```sql
SELECT *
FROM sportolo s
JOIN jelentkezes j ON s.id = j.sportolo_id;
```

Ha működik, utána építsd tovább.

### 5. Mindig nézd meg az eredményt

Ha lehetetlen eredmény jön ki, valószínűleg rossz a JOIN.

---

## 48. Mini próbaérettségi – adatbázis-kezelés

### Adatbázis: könyvtár

#### `olvaso`

| id | nev | varos | szuletesi_ev |
|---:|---|---|---:|
| 1 | Kovács Anna | Budapest | 2007 |
| 2 | Nagy Béla | Szeged | 2006 |
| 3 | Tóth Kata | Budapest | 2005 |
| 4 | Varga Márk | Pécs | 2007 |

#### `konyv`

| id | cim | szerzo | mufaj |
|---:|---|---|---|
| 1 | Egri csillagok | Gárdonyi Géza | regény |
| 2 | A Pál utcai fiúk | Molnár Ferenc | regény |
| 3 | SQL alapok | Kiss Péter | informatika |
| 4 | Webfejlesztés | Nagy Éva | informatika |

#### `kolcsonzes`

| id | olvaso_id | konyv_id | datum | napok |
|---:|---:|---:|---|---:|
| 1 | 1 | 1 | 2026-01-10 | 14 |
| 2 | 1 | 3 | 2026-02-12 | 7 |
| 3 | 2 | 2 | 2026-02-20 | 10 |
| 4 | 3 | 3 | 2026-03-01 | 21 |
| 5 | 3 | 4 | 2026-03-05 | 14 |

---

### Feladatok

1. Listázd az olvasók nevét város szerint rendezve!
2. Add meg ismétlődés nélkül a városokat!
3. Add meg a budapesti olvasókat!
4. Add meg, hány kölcsönzés történt összesen!
5. Add meg olvasónként a kölcsönzések számát!
6. Csak azok az olvasók jelenjenek meg, akik legalább 2 könyvet kölcsönöztek!
7. Add meg, mely olvasó mely könyvet kölcsönözte ki!
8. Add meg műfajonként a kölcsönzések számát!
9. Add meg az informatika műfajú könyveket kölcsönző olvasók nevét!
10. Add meg azt az olvasót, aki a leghosszabb időre kölcsönzött könyvet!

---

### Megoldások

#### 1.

```sql
SELECT nev, varos
FROM olvaso
ORDER BY varos, nev;
```

#### 2.

```sql
SELECT DISTINCT varos
FROM olvaso;
```

#### 3.

```sql
SELECT nev
FROM olvaso
WHERE varos = 'Budapest';
```

#### 4.

```sql
SELECT COUNT(*) AS kolcsonzesek_szama
FROM kolcsonzes;
```

#### 5.

```sql
SELECT o.nev,
       COUNT(k.id) AS kolcsonzesek_szama
FROM olvaso o
JOIN kolcsonzes k ON o.id = k.olvaso_id
GROUP BY o.id, o.nev;
```

#### 6.

```sql
SELECT o.nev,
       COUNT(k.id) AS kolcsonzesek_szama
FROM olvaso o
JOIN kolcsonzes k ON o.id = k.olvaso_id
GROUP BY o.id, o.nev
HAVING COUNT(k.id) >= 2;
```

#### 7.

```sql
SELECT o.nev AS olvaso,
       ko.cim AS konyv
FROM olvaso o
JOIN kolcsonzes k ON o.id = k.olvaso_id
JOIN konyv ko ON ko.id = k.konyv_id;
```

#### 8.

```sql
SELECT ko.mufaj,
       COUNT(k.id) AS kolcsonzesek_szama
FROM konyv ko
JOIN kolcsonzes k ON ko.id = k.konyv_id
GROUP BY ko.mufaj;
```

#### 9.

```sql
SELECT DISTINCT o.nev
FROM olvaso o
JOIN kolcsonzes k ON o.id = k.olvaso_id
JOIN konyv ko ON ko.id = k.konyv_id
WHERE ko.mufaj = 'informatika';
```

#### 10.

```sql
SELECT o.nev,
       ko.cim,
       k.napok
FROM olvaso o
JOIN kolcsonzes k ON o.id = k.olvaso_id
JOIN konyv ko ON ko.id = k.konyv_id
ORDER BY k.napok DESC
LIMIT 1;
```

Ha több azonos maximum is lehet:

```sql
SELECT o.nev,
       ko.cim,
       k.napok
FROM olvaso o
JOIN kolcsonzes k ON o.id = k.olvaso_id
JOIN konyv ko ON ko.id = k.konyv_id
WHERE k.napok = (
    SELECT MAX(napok)
    FROM kolcsonzes
);
```

---

## 49. Záró összefoglaló

Ha az adatbázis-kezelésből jó pontszámot akarsz, akkor ezt kell tudnod stabilan:

1. `SELECT`, `FROM`, `WHERE`
2. `ORDER BY`
3. `DISTINCT`
4. `CONCAT`
5. `COUNT`, `SUM`, `AVG`, `MIN`, `MAX`
6. `GROUP BY`
7. `HAVING`
8. `JOIN`
9. `LEFT JOIN`
10. `IS NULL`
11. dátumfüggvények: `YEAR`, `MONTH`, `DAY`
12. szövegfüggvények: `UPPER`, `LOWER`, `SUBSTRING`, `CHAR_LENGTH`
13. `CASE WHEN`
14. al-lekérdezések
15. `COUNT(DISTINCT ...)`

A legfontosabb mondat:

> SQL-ben nem parancsokat kell magolni, hanem kérdéseket kell táblákra, kapcsolatokra, szűrésekre és csoportokra bontani.

Ha ezt megtanulod, az adatbázisos rész nem félelmetes lesz, hanem az egyik legbiztosabb pontszerzési lehetőség.
