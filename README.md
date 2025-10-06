# Vooting Room 1.0 — Közösségi gondolkodó tér (MVP)

**Vooting Room** egy könnyű, nyílt forrású deliberációs eszköz:  
**kérdezni → érvelni → vitázni → szavazni → eredményt látni**.  
Kis terhelésre (≈ 11–211 egyidejű felhasználó) optimalizálva, gyorsan bevezethető workshopokra, pilotokra, közösségi egyeztetésekre.

- **Frontend:** Vanilla JavaScript (statikus HTML/CSS)  
- **Backend:** Python FastAPI + SQLite (később Postgresre bővíthető)  
- **Cél:** Azonnali részvételi élmény strukturált térben, **taggeléssel** (témakör/ügykör/szint), többféle **döntési móddal**.

---

## 0) Miről szól ez a projekt? (Lényeg)

A jelenlegi közbeszéd gyakran zajos és polarizált. A Vooting Room célja, hogy **kicsi, tiszta, gyakorlatias** eszközt adjon egy „**gondolkodó tér**” felépítéséhez, ahol:

- a kérdések **struktúráltan** jelennek meg,
- a vita **átlátható** szálban zajlik (thread),
- a döntés **többféle szavazási móddal** történik,
- a végeredmény **összegzett és visszakereshető**,
- minden kérdés **be van ágyazva** a nagyobb gondolkodási térbe (tag-ek).

Ez az MVP nem egy „kész demokrácia-platform”, hanem egy **élő tanulómező** alap-modulja.

---

## 1) Ki használja és mire?

**Felhasználók (résztvevők)**  
- Kérdéseket tesznek fel, szavaznak, hozzászólnak.  
- Látják, hova **taggelt** a kérdés (témakör/ügykör/szint).

**Szervezők (facilitátorok)**  
- Kérdések minőségi felvetése (AI-tisztítás később).  
- Átlátható vitaterek nyitása, időablakok beállítása.  
- Eredmények publikálása, exportálása (később).

**Üzemeltetők**  
- Telepítés Hetznerre vagy bármely Ubuntu szerverre.  
- Mentés, frissítés, skálázás.

---

## 2) Fogalmak gyorsan

- **Kérdés:** A deliberáció egysége (cím, leírás, tag-ek, típus).  
- **Vooting Room:** Szavazási felület a kérdéshez.  
- **Threadroom:** Könnyű vitaszál, hozzászólásokkal.  
- **Tag-ek:** A gondolkodási tér struktúrája (pl. `#oktatás`, `#AI`, `helyi`, `országos`).  
- **Eredmény:** Összegzett kimenet (számlálók, átlag, hisztogram), publikálható.

---

## 3) Döntési módok (MVP)

1. **Igen / Nem** – egyszerű eldöntendő kérdésekre.  
2. **Egy opció a többből** – klasszikus választás.  
3. **Több opció választható** – támogatási preferenciák mérése.  
4. **Skála (1–100)** – érzékenység/intenzitás (átlag + eloszlás).  
5. **Pontelosztás (100 pont)** – erősebb preferenciák kimutatása.

> A későbbi verziókban jöhet: rangsoroló szavazás, delegált (liquid) szavazás, deliberatív mintavétel, stb.

---

## 4) Hogyan használjam résztvevőként?

1. **Nyisd meg az oldalt.**  
2. **Nézd át a kérdéseket** (Kérdések panel).  
3. **Válassz egy kérdést**, és kattints a „Megnyitás”-ra.  
4. **Olvasd el a leírást és tag-eket.**  
5. **Szavazz** (a kérdés típusának megfelelően).  
6. **Nézd meg az eredményeket** (számlálók/átlag).  
7. **Szólj hozzá** (Threadroom) – érvelés, hivatkozás, tiszta kérdések.

**Alapelv:** nem a személy a téma, hanem a **valóság pontosítása**.

---

## 5) Hogyan használjam szervezőként?

1. **Fogalmazd meg a kérdést**: rövid cím + lényegre törő leírás.  
2. **Taggeld**: témakör/ügykör/szint (pl. `#oktatás,#AI | helyi`).  
3. **Válaszd ki a szavazási típust** (lásd fent).  
4. **Adj meg opciókat** (ha szükséges).  
5. **Nyisd meg a szavazást**, állíts időablakot (később).  
6. **Moderáld a Threadroom-ot** (kulturált vita, témán tartás).  
7. **Zárd le**, publikáld az eredményt + következő lépést.

**Tipp:** Egy jó kérdés egyértelmű, kontextusos, és **nem ideológiai lózung**.

---

## 6) Telepítés és futtatás

### 6.1. Gyors indítás (helyben)

**Backend**
```bash
cd backend
python3 -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
uvicorn app:app --reload --host 0.0.0.0 --port 8000
