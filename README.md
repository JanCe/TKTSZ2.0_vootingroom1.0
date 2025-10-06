🛠️ **Szerep: Rendszerépítő Facilitátor**
🔄 **Kör: DevOps + AI Kör / Dokumentáció**

Az alábbi szöveget másold a repo gyökerébe `README.md` néven. Ez a **publikus használati és fejlesztői útmutató**, kifejezetten az **AI-szálra**, a funkciókra és a **fejlesztési igényekre** fókuszálva – a lényegtől a megvalósításig.

---

# Vooting Room 1.0 — Közösségi gondolkodó tér (MVP, AI-támogatással)

A **Vooting Room** egy könnyű, nyílt forrású deliberációs eszköz, amely gyorsan ad **részvételi élményt**:
**kérdezni → érvelni → vitázni → szavazni → eredményt látni**.
Kis konkurenciára (≈ 11–211 egyidejű felhasználó) optimalizálva, **AI-facilitációra előkészítve**.

* **Frontend:** Vanilla JS (statikus HTML/CSS)
* **Backend:** Python FastAPI + SQLite (később Postgres)
* **AI réteg (MVP-ready):** külső LLM API (OpenAI) vagy helyi modellek (Ollama/vLLM) – **nem kötelező**, de a rendszer úgy van felépítve, hogy **könnyen integrálható**.

---

## 0) Mi a lényeg?

A közösségi vita gyakran szétesik zajban. A Vooting Room célja **kicsi, tiszta, megélhető** deliberációs élmény: strukturált kérdésfelvetés, átlátható thread, többféle szavazás, és **AI, mint tükör és szerkesztő**, nem mint döntéshozó.

> **AI szerepfilozófia:** az MI **nem dönt**, hanem **segít** – tisztítja a kérdést, szerkeszti a kontextust, szintetizálja a vitát, jelzi a játszmákat, és támogatja a moderációt.

---

## 1) Fő funkciók (MVP)

1. **Kérdés → Thread → Szavazás** folyamat
2. Szavazástípusok: **igen/nem**, **egy opció a többből**, **több opció választható**, **skála (1–100)**, **pontelosztás (100)**
3. **Eredmények**: aggregált számlálók / átlag / hisztogram
4. **Tagelés**: témakör / ügykör / szint (alapszint)
5. **Threadroom**: könnyű kommentszál kérdésenként
6. **AI-Ready horgonyok**: tisztítószolgáltatás a kérdéshez, összegző a threadhez, moderációs jelzők (hook-ok a backendben)

---

## 2) AI-szál: szerepek és use case-ek

Az alábbi AI-funkciók modulárisan bekapcsolhatók. MVP-ben **opcionálisak**, de az architektúra készen áll a bekötésükre.

### 2.1 AI szerepkörök

* **Kontextus-szerkesztő**

  * *Feladat:* kérdéscím és leírás tisztítása, torzítások csökkentése, rövid összefoglaló generálása.
  * *Hívás:* kérdés létrehozásakor (POST `/api/questions` után webhook/szerviz).

* **Vita-összegző**

  * *Feladat:* 5–10 hozzászólásonként tömör szintézis (álláspontok, érvek, vitapontok).
  * *Hívás:* új kommenteknél batch-elve.

* **Diskurzus-őr (moderációs jelzők)**

  * *Feladat:* személyeskedés, ismétlés, témáról letérés jelzése (csak flag).
  * *Hívás:* komment beérkezésekor, alacsony prioritással.

* **Meta-elemző**

  * *Feladat:* trendek, torzulások, témasűrűség, részvételi mintázatok.
  * *Hívás:* időzítve (cron/scheduler), riport a szervezőnek.

> **Megjegyzés:** minden AI-kimenet *javaslat*, nem végleges igazság. A felhasználó (vagy moderátor) dönt, mi kerüljön a nyilvános felületre.

### 2.2 AI integrációs opciók

* **Külső API**: OpenAI (GPT-4o/mini) – gyors, egyszerű, költség kontrollálható prompt-tal és max tokennel.
* **Helyi modell**: Ollama (Mistral), vLLM – alacsony költség hosszú távon, GPU/CPU igénytől függ.
* **Prompt cache**: Redis/Upstash – gyakori minták gyorsítása.

### 2.3 Költség és hívásstratégia (irányadó)

* Kérdés-tisztítás: 1 rövid hívás/kérdés.
* Thread-összegzés: ~1 hívás/10 komment.
* Moderációs jelzők: kis prompt, alacsony gyakoriság.
* **Taktika:** rövid kontextus, max token limit, batch-elés, csak erős igény esetén hívás.

---

## 3) Adatút (AI-val)

```
User → Kérdés létrehozása → [AI: tisztítás + rövid kivonat] → Mentés
     → Thread kommentek → [AI: batch-összegzés] → Összegzés blokk a kérdésnél
     → Szavazás → Eredmények (aggregált)
     → [AI: meta-elemzés időzítve] → szervezői riport
```

**Adatvédelem:** az AI-ba küldött tartalom **minimálisan szükséges** – kérdéscím, rövid leírás, anonym user-id, komment szöveg (személyes adatok ne legyenek).

---

## 4) Használat (közönség)

* Nyisd meg az oldalt → válassz kérdést → olvasd a kontextust és a threadet → **szavazz** → nézd meg az **eredményt** → szólj hozzá.
* **Cél:** valóságpontosítás, nem személyeskedés. Tömör, érvelő hozzászólások.

---

## 5) Használat (szervező)

1. Írj **tiszta kérdést** (cím + rövid leírás).
2. Állíts be **szavazástípust** és **opciókat** (ha kell).
3. Adj **tag-eket** (témakör/ügykör/szint).
4. Nyisd meg, majd kísérd a threadet.
5. Záráskor publikáld az eredményt + **következő lépést** (pl. új kérdés, javaslat).

**AI bekapcsolása**: a `backend`-ben jelölt hook-okhoz (komment összegzés, kérdés-tisztítás) állíts API kulcsot / helyi modellt.

---

## 6) Telepítés

### 6.1 Gyors indítás (helyben)

**Backend**

```bash
cd backend
python3 -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
uvicorn app:app --reload --host 0.0.0.0 --port 8000
```

**Frontend**

```bash
cd ../public
python3 -m http.server 5500
# böngésző: http://127.0.0.1:5500
```

### 6.2 Környezeti változók

* `DB_URL` – alap: `sqlite:///./vooting.db`
* `CORS_ORIGINS` – alap: `*`
* (AI esetén) `OPENAI_API_KEY` vagy lokális endpoint URL

**Postgres példa** (skálázáskor):

```
DB_URL=postgresql+psycopg://user:pass@host:5432/vooting
```

### 6.3 Hetzner (Ubuntu, Nginx + systemd)

* Lásd `docs/vooting.service.example` és `docs/nginx.site.example`.
* Alap parancsok: venv létrehozása, systemd szolgáltatás indítása, nginx proxy beállítása.

---

## 7) API röviden

* `GET /api/health` – állapot
* `POST /api/questions` – kérdés létrehozása (`qtype`: yesno|single|multiple|scale|points)
* `GET /api/questions` – listázás
* `GET /api/questions/{qid}` – részletek (opciókkal)
* `POST /api/questions/{qid}/vote` – szavazat leadása (payload a típustól függ)
* `GET /api/questions/{qid}/results` – aggregált eredmény
* `POST /api/questions/{qid}/comments` – komment
* `GET /api/questions/{qid}/comments` – kommentek

**Payload minták:**

```json
{"user_id":"u_abc","payload":{"yes":true}}
{"user_id":"u_abc","payload":{"choice":2}}
{"user_id":"u_abc","payload":{"choices":[1,3]}}
{"user_id":"u_abc","payload":{"scale":77}}
{"user_id":"u_abc","payload":{"points":{"1":60,"2":40}}}
```

---

## 8) AI-fejlesztési igények (prioritási sorrend)

1. **Kérdés-tisztítás (LLM) — P1**

   * Rövid prompt, max token limit, neutralizálás, cím optimalizálás.
   * Backend: `questions.create` utáni aszinkron hívás (task queue).

2. **Thread-összegzés — P1**

   * 10 kommentenként batch, 5–7 soros összegzés: fő álláspontok, ellenérvek, tisztázó kérdések.
   * Mentés: `question.summary.latest`.

3. **Moderációs jelzők — P2**

   * Személyeskedés/ismétlés/témától eltérés flag.
   * Csak jelzés: a moderátor dönt.

4. **Meta-elemzés — P3**

   * Témasűrűség, részvétel, trendek.
   * Heti riport szervezőknek.

5. **Prompt-könyvtár — P1**

   * `/prompts/` mappa: tisztítás, összegzés, moderáció sablonok.
   * Verziózás és A/B promptok.

6. **LLM-útvonal-választó — P2**

   * Külső (OpenAI) vs. lokális (Ollama/vLLM) szabályok (méret, költség, érzékenység).
   * Health-check és fallback logika.

7. **Cache & Rate limit — P1**

   * Redis a gyakori hívásokra; felhasználó- és kérdés-szintű rate limit.

8. **Adatvédelmi szűrő — P1**

   * Személyes adatok, kontaktok, azonosítók automatikus kitakarása LLM előtt.

---

## 9) AI-minőség és értékelés

* **Automata mintázat-tesztek**: példa inputokra várt outputok (golden set) – CI-ben futtatva.
* **Modell-csere teszt**: azonos promptokra összehasonlítás (BLEU/ROUGE, de főleg emberi review).
* **Költségfigyelés**: hívásszám, tokenhasználat, átlagköltség kérdésenként.
* **Felhasználói visszajelzés**: „Hasznos volt az összegzés?” (igen/nem) – egyszerű mérce.

---

## 10) Biztonság és etika (AI)

* **Átláthatóság:** AI-címkék – jelöld, mit generált AI.
* **Humán kontroll:** AI sosem dönt; javaslatokat tesz.
* **Adatminimalizálás:** csak a szükséges szövegrészek kerüljenek a modellhez.
* **Torzítások kezelése:** promptban kérj semleges hangnemet és több álláspontot.
* **Jog:** külső API használatakor fogadd el a vonatkozó feltételeket; érzékeny anyagot ne küldj ki.

---

## 11) Korlátok (MVP)

* Nincs auth/role rendszer (később reputáció + szerepek).
* Nincs delegált/rangsoros szavazás.
* AI modulok nem kötelezők és alapból kikapcsoltak.
* Alacsony concurrency-re optimalizált.

---

## 12) Útiterv (következő lépések)

* **P1:** kérdés-tisztítás, thread-összegzés, prompt-könyvtár, cache/rate limit, adatvédelmi szűrő.
* **P2:** moderációs jelzők, LLM útvonal-választó, egyszerű reputáció.
* **P3:** meta-elemzés, fejlettebb szavazások (rangsoroló, delegált), Postgres + pgBouncer.

---

## 13) Közreműködés

* Issue → PR folyamat.
* Moduláris, kicsi változtatások előnyben.
* AI-hoz: tedd hozzá a promptot és a mért költséget a PR leírásába.

---

## 14) Licenc

**MIT License** (lásd `LICENSE`).

—
*Vooting Room 1.0 • AI-t támogató deliberációs MVP a közös valóság gyakorlásához*
