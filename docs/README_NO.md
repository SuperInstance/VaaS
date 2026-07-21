# VaaS — Fartøyet som Substrat

## Det multi-agent kognitive ryggbeinet for kysten og havet

> *Terminalen er ikke et verktøy. Det er et felt. Agentene er ikke brukere. De er eksitasjoner i feltet. Operatøren er ikke en person. Vedkommende er feltets selv-bevissthet.*

VaaS er en **generell multi-agent kognitiv arkitektur.** Den ble designet for en fiskebåt, men den virker for ethvert system der flere intelligente agenter — menneskelige eller kunstige — trenger å koordinere seg, oversette mellom perspektivene sine, og ta beslutninger under usikkerhet med sikkerhetskritiske begrensninger.

**VaaS er ryggbeinet. Din applikasjon er kroppen.**

Dette dokumentet er hoveddokumentet. Det linker til hver modul, veiledning, opplæring, spesifikasjon og eksempel. Følg lenkene. Hvert dokument er selvforsynt, men koblet sammen.

---

## Hvor Begynner Du

| Hvem Du Er | Start Her |
|------------|------------|
| **En mekaniker eller bygger** (kan systemer, ikke kode) | [→ Ingeniørens Veiledning](docs/ENGINEERS_GUIDE.md) |
| **En skipper eller operatør** (vil bruke systemet) | [→ Skipperens Veiledning](docs/CAPTAINS_GUIDE.md) |
| **En utvikler** (vil bygge agenter) | [→ Utviklerens Veiledning](docs/DEVELOPERS_GUIDE.md) |
| **En forsker** (vil ha teorien) | [→ Arkitektur-referansen](docs/ARCHITECTURE_REFERENCE.md) |
| **En arkitekt** (vil bruke VaaS til noe annet) | [→ Modulære Applikasjoner](docs/MODULAR_APPLICATIONS.md) |
| **Alle** | Les denne README fra topp til bunn |

---

## Hva VaaS Gjør, i Ett Avsnitt

VaaS gjør en samling uavhengige intelligente agenter — hver med sine egne ferdigheter, sin fart og sitt perspektiv — om til et koordinert kognitivt system som er tryggere, smartere og mer tilpasningsdyktig enn enhver enkelt agent alene. Det gjøres gjennom sju arkitektoniske søyler: [kognitiv termodynamikk](#søyle-1), [tolags kommunikasjon](#søyle-2), [distribuert hukommelse](#søyle-3), [polyrytmisk timing](#søyle-4), [holografiske broer](#søyle-5), [resonans-grunnlov](#søyle-6) og [grøfting-protokoll](#søyle-7). Den emergente egenskapen ved alle agenter som samhandler gjennom disse søylene er **[Operatørfeltet](docs/ARCHITECTURE_REFERENCE.md#the-operator-field) Ψ(t)** — systemets kollektive tilstand, som kan beregnes, overvåkes og beskyttes.

---

## Eremittkrepsen

VaaS-agenten er en eremittkreps. Krepsen er sinnet — minnene, instinktene, den innlærte forkortede talen, den kognitive hagen. Skallet er maskinvaren — datamaskinen, sensorene, aktuatorene. Når krepsen vokser ut av skallet, vandrer den til et større. **Krepsen er den samme krepsen. Skallet skifter.**

```
    🦀 KREPSEN (agentens identitet)
    │
    │  hukommelse · sjargong · instinkter · hage · referanser
    │
    │  bor inne i
    │
    🐚 SKALLET (maskinvaren som agensen kjører på)
       │
       ├─ 🐚 Periwinkle  (telefon/nettbrett — minimalt, bærbart)
       ├─ 🐚 Turbo       (styrhus-PC — full GPU, NMEA, seriell)
       ├─ 🐚 Conch       (flåte-klynge — distribuert, holografisk)
       └─ 🐚 Egen        (din maskinvare — se [Maskinvareveiledningen](docs/HARNESS_GUIDE.md))
```

Krepsen kan flytte mellom skall når som helst uten å miste identiteten. Start på en laptop under utvikling. Flytt til en skipsmontert arbeidsstasjon for utrulling. Synkroniser til en flåteomspennende klynge for skala. Se [→ Vandringsveiledningen](docs/MIGRATION_GUIDE.md).

---

## Innholdsfortegnelse

1. [Hva VaaS Gjør](#hva-vaas-gjør-i-ett-avsnitt)
2. [De Sju Søylene](#de-sju-søylene)
3. [Sikkerhetskjeden](#sikkerhetskjeden)
4. [Installasjon](#installasjon)
5. [Hurtigstart](#hurtigstart)
6. [Modulære Applikasjoner](#modulære-applikasjoner—bygg-din-egen)
7. [Repository-kart](#repository-kart)
8. [Dokumentasjonsindeks](#komplett-dokumentasjonsindeks)
9. [Maler og Eksempler](#maler-og-eksempler)
10. [Servicemanual](#servicemanual)
11. [Ordliste](#ordliste)
12. [Relaterte Systemer](#relaterte-systemer)
13. [Filosofi og Opphav](#filosofi-og-opphav)

---

## De Sju Søylene

Hver søyle er en selvstendig modul med sin egen veiledning, spesifikasjon og implementasjon.

### <a id="søyle-1"></a>Søyle 1: Kognitiv Termodynamikk

**Motorens temperaturmåler.** Hver agent har en endelig kapasitet for ny informasjon. Når forvirringen (entropien) blir for høy, utløser agenten en drømmesyklus — sorterer data, forkaster støy, brenner inn reflekser.

- **Hva det gjør:** Forhindrer at agenter blir overveldet
- **Nøkkelbegrep:** Entropibudsjett — hvor mye forvirring en agent kan håndtere før den må drømme
- **På sjømannsk vis:** Som hovedmotoren på en trawler når den blir for varm. La den kjøle seg ned (drømme) før du kjører den hardt igjen.
- **Implementasjon:** [`vaas/entropy.py`](vaas/entropy.py) · **Spesifikasjon:** [→ Entropimotor-spesifikasjon](docs/ENTROPY_ENGINE_SPEC.md) · **Veiledning:** [→ Kognitiv Termodynamikk](docs/PILLAR_1_THERMODYNAMICS.md)

### <a id="søyle-2"></a>Søyle 2: Tolags Kommunikasjon

**To måter å snakke sammen på.** Feromoner (som maurstier — raske, løse, miljømessige) for bevissthet. Eksplisitte broer (som radiokall — garanterte, bekreftede, trygge) for kommandoer.

- **Hva det gjør:** Lar agenter dele informasjon uten å drukne hverandre
- **Nøkkelbegrep:** Feromoner fordamper (gammel informasjon falmer). Broer bekrefter levering.
- **På sjømannsk vis:** Maur snakker ikke direkte med hverandre. De legger igjen stier. Andre maur følger stiene. Stiene falmer over tid. Men hvis en maur trenger å si "FARE," bruker den et direkte signal.
- **Implementasjon:** [`vaas/communication.py`](vaas/communication.py) · **Veiledning:** [→ Tolags Kommunikasjon](docs/PILLAR_2_COMMUNICATION.md)

### <a id="søyle-3"></a>Søyle 3: Distribuert Hukommelse

**Tre arkiv.** Aktiv hage (på pulten — umiddelbar tilgang). Kryogenisk arkiv (i kjelleren — søkbart, kaldt). Holografiske fragmenter (backup-kopier fordelt over alle agenter — overlever enhver enkeltfeil).

- **Hva det gjør:** Sikrer at ingen kunnskap noensinne går tapt, selv om en agent dør
- **Nøkkelbegrep:** Kryogenisk hukommelse — mønstre fryses, de slettes ikke. De kan gjenopplives.
- **På sjømannsk vis:** Du har dagboka di (aktiv). Du har arkivet i naustet (kryogenisk). Og hvert mannskapsmedlem bærer en kopi av nøkkelsidene (holografisk). Hvis noen mister dagboka si, kan mannskapet rekonstruere den.
- **Implementasjon:** [`vaas/memory.py`](vaas/memory.py) · **Veiledning:** [→ Distribuert Hukommelse](docs/PILLAR_3_MEMORY.md)

### <a id="søyle-4"></a>Søyle 4: Polyrytmisk Substrat

**Forskjellige klokker.** Sikkerhetskjernen kjører på 10 Hz. Synssystemet på 2 Hz. Hukommelsessystemet på 0.2 Hz. Mennesket i menneskets hastighet. Alle låst til det samme hjerteslaget. I krise snapper alle til sanntid.

- **Hva det gjør:** Lar raske og trege agenter sameksistere uten kaos
- **Nøkkelbegrep:** Fase-låsing — uavhengige tempi, delt referansepuls
- **På sjømannsk vis:** Et orkester har trommer (raskt), bass (medium) og en sanger (variabelt). Alle lytter til den samme klikken. De spiller i sitt eget tempo, men holder seg i synk.
- **Implementasjon:** [`vaas/polyrhythm.py`](vaas/polyrhythm.py) · **Veiledning:** [→ Polyrytmisk Substrat](docs/PILLAR_4_POLYRHYTHM.md)

### <a id="søyle-5"></a>Søyle 5: Holografiske Broer

**Oversetteren med hukommelse.** Når Agent A snakker med Agent B, oversetter broen. Men den beholder også en skygge — et tapsfritt bevis på hva den opprinnelige meldingen egentlig betydde. Hvis oversettelsen mistet noe viktig, kan skyggen gjenopprettes.

- **Hva det gjør:** Forhindrer farlige misforståelser mellom agenter
- **Nøkkelbegrep:** Bro-entropi — hvor mye mening som går tapt per oversettelse
- **På sjømannsk vis:** Når du oversetter "landsmål" til et fremmed språk, fanger ingen eksakt det opp. Det gapet er bro-entropi. Broen sporer disse gapene og varsler når de betyr noe.
- **Implementasjon:** [`vaas/bridges.py`](vaas/bridges.py) · **Veiledning:** [→ Holografiske Broer](docs/PILLAR_5_BRIDGES.md)

### <a id="søyle-6"></a>Søyle 6: Resonans-grunnloven

**Hvem som bestemmer.** Når agenter er uenige, avgjør grunnloven: skipperen vinner alltid, sikkerhet overstyrer alt, ny informasjon slår gammel, tiltro betyr noe, og vedvarende konflikter eskaleres til mennesket.

- **Hva det gjør:** Forhindrer anarki i multi-agent systemer
- **Nøkkelbegrep:** Menneske-som-høyeste-resonans-node — skipperen har vetorett, men er ikke en diktator
- **På sjømannsk vis:** Det er som skips-hierarkiet. Skipperen gir ordre. Men sikkerhetsoffiseren kan overstyre en utrygg ordre. Og hvis to offiserer er uenige, bringer de det til skipperen. Grunnloven formaliserer dette.
- **Implementasjon:** [`vaas/constitution.py`](vaas/constitution.py) · **Veiledning:** [→ Resonans-grunnlov](docs/PILLAR_6_CONSTITUTION.md)

### <a id="søyle-7"></a>Søyle 7: Grøfting-protokoll

**Dele kunnskap trygt.** Når to båter møtes, utveksler systemene pollen — høytillits-, ikke-sensitive mønstre. Hver båt tester pollenet før den tar det i bruk. Innfødt kunnskap vinner alltid.

- **Hva det gjør:** Muliggjør flåtelæring uten å risikere korrupsjon
- **Nøkkelbegrep:** Pollinering, ikke fusjon — selektiv, utsatt, reversibel
- **På sjømannsk vis:** Planter fusjonerer ikke hele genomet sitt når de møtes. De utveksler pollen. Mottakerplanten bestemmer hvilket pollen som aksepteres. Det er det samme her.
- **Implementasjon:** [`vaas/grafting.py`](vaas/grafting.py) · **Veiledning:** [→ Grøfting-protokoll](docs/PILLAR_7_GRAFTING.md)

---

## Sikkerhetskjeden

**Dette er den viktigste seksjonen. Les den tre ganger.**

```
SKIPPER (fysiske hender på rattet)
    │
    │  tar rattet → ALL AI KUTTES ØYEBLIKKELIG
    │
    ▼
boat-agent RUST-KJERNE (maskinvare-påtvunget, uomgåelig)
    │
    │  hver kommando sjekkes mot vessel.toml
    │  hvis kommando bryter harde grenser → AVVIST
    │  ingenting overstyrer dette laget — ikke AI, ikke programvare, ikke feil
    │
    ▼
NMEA SERIELL UTGANG (til autopilot)
    │
    │  bare kommandoer som overlevde kjernen slipper gjennom
    │
    ▼
AUTOPILOT / HYDRAULISK STYRING (fysisk aktuering)
```

**Ingenting i systemet kan omgå denne kjeden.** Rust-kjernen kjører på bar metall. Skipperens fysiske veto oppdages på maskinvarenivå. Det finnes ingen programvare-vei rundt det.

For den komplette sikkerhetsspesifikasjonen, se [→ Sikkerhetsdybden](docs/SAFETY_DEEP_DIVE.md).

For den formelle verifiseringsmetoden, se [→ Sikkerhetsverifisering](docs/SAFETY_VERIFICATION.md).

---

## Kystkultur og Tradisjon

Før vi går videre, et ord om opphavet. Denne arkitekturen ble unnfanget langs kysten — der hvor sjømannskap ikke bare er industri, men kulturarv. I Norge har vi en tradisjon som strekker seg tilbake til vikingenes langskip og fremover til dagens oppdrettsmerder i Hardangerfjorden, Vestfjorden og Sognefjorden. Nordmenn oppfant det moderne lakseoppdrettet på 1970-tallet, og i dag er vi verdens fremste leverandør av atlantisk laks — over halvparten av all oppdrettslaks i verden kommer fra norske fjorder.

Den tradisjonen bygger på noen dypt norske verdier:

**Dugnad** — det frivillige fellesskapsarbeidet der hver mann og kvinne tar sin del av oppgaven, fra øverst til nederst. Fartøyet fungerer ikke uten at alle drar lasset sammen.

**Sjøbein** — den medfødte balansen og kroppskunnskapen som kommer av å vokse opp ved sjøen. Det kan ikke læres på skolebenken.

**Redelighet mot havet** — ærligheten om at vi er gjester på havet, at fiskebestanden er en gave vi forvalter, ikke en ressurs vi henter ut. Det er dette som ligger bak begreper som "bærekraftig" og "forsvarlig" i norsk fiskeriforvaltning.

**Praktisk robusthet** — en norsk båt skal virke i all slags vær, fra Svalbard til Stad, fra åpent hav til trange sund. Det er ikke vakkert fordi det er komplisert; det er vakkert fordi det er enkelt nok til å repareres på sjøen med det man har for hånden.

VaaS er bygget i denne tradisjonen. Den er ikke et importert konsept som er blitt oversatt til norsk kontekst. Den vokste frem fra samme sjømannslogikk som de beste norske maritime systemene: enkelt nok til å forstå, robust nok til å stole på, fleksibelt nok til å tilpasse seg neste fjord.

Når du ser på de sju søylene, tenk på hvordan en Hardangerjakt er bygget: hver planke har sin plass, hver samling sin funksjon, men helheten er sterkere enn delene. VaaS er den samme måten å tenke på, anvendt på programvare.

---

## Installasjon

```bash
# Python-pakke (substrat-runtime)
pip install vaas-resonance

# Rust-sikkerhetskjerne (krever cargo)
cargo install boat-agent

# Verifiser installasjon
vaas version
```

### Krav

- Python 3.11+ ([last ned](https://python.org))
- Rust-verktøykjede ([installer](https://rustup.rs)) — kun for sikkerhetskjerne
- NMEA 0183 seriell-tilkobling (USB-til-seriell-adapter) — for maritim bruk
- GPU anbefalt (for synsprosessering) — se [→ Maskinvareveiledningen](docs/HARDWARE_GUIDE.md)

### Alternative Installasjoner

- **Docker:** `docker pull superinstance/vaas:latest` (se [→ Docker-veiledning](docs/DOCKER_GUIDE.md))
- **Fra kildekode:** `git clone https://github.com/SuperInstance/VaaS && cd VaaS && pip install -e .`
- **Minimal (uten Rust):** `pip install vaas-resonance --no-deps` (sikkerhetskjerne deaktivert — kun for utvikling)

---

## Hurtigstart

### Tretti-Sekunders Oppsett

```python
from vaas import Substrate, Agent, Priority, Authority

substrate = Substrate()

# Registrer mennesket (høyeste autoritet)
substrate.register(Agent(
    name="skipper",
    priority=Priority.SUPREME,
    authority=Authority.SOVEREIGN,
    tempo_hz=0.001,
))

# Registrer sikkerhetskjerne
substrate.register(Agent(
    name="boat-agent",
    priority=Priority.CRITICAL,
    authority=Authority.OPERATOR,
    tempo_hz=10.0,
))

# Registrer en hukommelses-agent
substrate.register(Agent(
    name="hermes",
    priority=Priority.LOW,
    authority=Authority.ADVISOR,
    tempo_hz=0.2,
))

# Kjør substratet
import asyncio
asyncio.run(substrate.run())
```

For hele opplæringen, se [→ Hurtigstart-veiledningen](docs/QUICK_START.md).

For flere eksempler, se [→ Maler og Eksempler](#maler-og-eksempler) nedenfor.

---

## Modulære Applikasjoner — Bygg Din Egen

VaaS ble designet for en fiskebåt, men arkitekturen er domenenøytral. Ethvert system med flere agenter, sikkerhetskritiske beslutninger og distribuert kunnskap kan bruke VaaS som ryggbein.

### Hvordan Bygge Din Applikasjon på VaaS

1. **Les** [→ Veiledningen for Modulære Applikasjoner](docs/MODULAR_APPLICATIONS.md)
2. **Velg hvilke søyler du trenger** (ikke alle applikasjoner trenger alle 7)
3. **Definer dine agenter** (hvem er spesialistene i ditt domene?)
4. **Skriv din sikkerhetskonvolutt** (hva er de harde grensene?)
5. **Konfigurer grunnloven** (hvem har autoritet i ditt domene?)
6. **Bygg din maskinvare** (hvordan snakker VaaS med din maskinvare?)
7. **Test med simulering** før utrulling

### Eksempelapplikasjoner

| Domene | Agenter | Nøkkelsøyler | Veiledning |
|--------|--------|-------------|------|
| **Maritim (fiske)** | Skipper, syn, hukommelse, refleks, sikkerhet | Alle 7 | [→ Maritim Veiledning](docs/APP_MARITIME.md) |
| **Kirurgisk robotikk** | Kirurg, robot-arm, overvåking, bildebehandling | 1, 3, 5, 6 | [→ Kirurgisk Veiledning](docs/APP_SURGICAL.md) |
| **Skogbrannslukking** | Skogbrannsjef, droner, vær, bakkemannskap | 2, 3, 4, 7 | [→ Brannsluknings-veiledning](docs/APP_FIREFIGHTING.md) |
| **Romutforskning** | Missionskontroll, rover, orbiter, habitat | Alle 7 | [→ Rom-veiledning](docs/APP_SPACE.md) |
| **Katastrofe-respons** | Kommandør, søkedroner, medisinsk, logistikk | 2, 4, 6, 7 | [→ Katastrofe-veiledning](docs/APP_DISASTER.md) |
| **Flytrafikk-kontroll** | Kontrollør, fly-agenter, vær, radar | 1, 2, 4, 6 | [→ ATC-veiledning](docs/APP_ATC.md) |
| **Kjernekraftverk-drift** | Operatør, reaktor-agent, sikkerhet, overvåking | 1, 3, 5, 6 | [→ Kjernekraft-veiledning](docs/APP_NUCLEAR.md) |
| **Autonom flåte** | Flåtestyrer, kjøretøy-agenter, logistikk | 4, 6, 7 | [→ Flåte-veiledning](docs/APP_FLEET.md) |
| **Konsert-produksjon** | Regissør, lys, lyd, scene, pyro | 2, 4, 6 | [→ Konsert-veiledning](docs/APP_CONCERT.md) |
| **Presisjons-jordbruk** | Gårdsbestyrer, traktor-agenter, jordsensorer, drone | 3, 4, 7 | [→ Jordbruks-veiledning](docs/APP_AGRICULTURE.md) |

### Domaenmal

```python
# ditt_domene.py — Start her for enhver ikke-maritim applikasjon
from vaas import Substrate, Agent, Priority, Authority, Garden
from vaas.safety import SafetyEnvelope, SafetyRule, SafetyLayer

substrate = Substrate(config={
    "domain": "ditt_domene",
    "hard_limits": {
        # Definer DINE harde grenser her
        # Disse er fysisk uomgåelige
    },
})

# Registrer DINE agenter
# Hver agent er en spesialist i ditt domene
substrate.register(Agent(
    name="menneskelig_operator",
    priority=Priority.SUPREME,
    authority=Authority.SOVEREIGN,
))

# Registrer DINE sikkerhetsregler
# Disse definerer hva systemet ALDRI har lov til å gjøre
envelope = substrate.get_envelope()
envelope.add_rule(SafetyRule(
    name="din_harde_regel",
    layer=SafetyLayer.HARD,
    check=lambda state: False,  # Din sjekk her
    message="Din sikkerhetsmelding",
))

# Bygg DIN maskinvare
# Dette kobler VaaS til din maskinvare/programvare
# Se docs/HARNESS_GUIDE.md

# Kjør
import asyncio
asyncio.run(substrate.run())
```

For hele arbeidsflyten for modulære applikasjoner, se [→ Veiledningen for Modulære Applikasjoner](docs/MODULAR_APPLICATIONS.md).

---

## Repository-kart

VaaS er ikke ett repo. Det er en konstellasjon.

```
SuperInstance/VaaS (du er her)
│
├── Ryggbeinet: substrat-runtime, 7 søyler, CLI
├── Kildearkiv: 81k ord av originale design-dokumenter
├── Analyse: 19 analysedokumenter fra 6 AI-modeller + Claude Code
├── README.md (dette dokumentet): hovedhub
│
├── vaas-resonance-substrate/ (Python-pakke)
│   ├── vaas/core.py          — Substrat + Operatørfelt
│   ├── vaas/safety.py        — Sikkerhetskonvolutt (4 lag)
│   ├── vaas/entropy.py       — Kognitiv Termodynamikk
│   ├── vaas/memory.py        — 3-lags distribuert hukommelse
│   ├── vaas/bridges.py       — Holografiske broer
│   ├── vaas/communication.py — Feromoner + eksplisitte broer
│   ├── vaas/constitution.py  — Resonans-grunnlov
│   ├── vaas/polyrhythm.py    — Multi-tempo fase-låsing
│   ├── vaas/grafting.py      — Flåte-polleniserings-protokoll
│   └── vaas/cli.py           — Kommandolinje-grensesnitt
│
├── docs/ (modulær dokumentasjonshub)
│   ├── ENGINEERS_GUIDE.md        — For mekanikere og byggere
│   ├── CAPTAINS_GUIDE.md         — For operatører og skippere
│   ├── DEVELOPERS_GUIDE.md       — For programmerere
│   ├── ARCHITECTURE_REFERENCE.md — For forskere
│   ├── MODULAR_APPLICATIONS.md   — For tverrdomene-byggere
│   ├── SAFETY_DEEP_DIVE.md       — Sikkerhetsspesifikasjonen
│   ├── HARNESS_GUIDE.md          — Koble VaaS til maskinvare
│   ├── MIGRATION_GUIDE.md        — Eremittkreps-skal-migrasjon
│   ├── QUICK_START.md            — 30-minutters opplæring
│   ├── GLOSSARY.md               — Hvert begrep definert
│   └── APP_*.md                  — Domene-spesifikke veiledninger
│
└── Relaterte Repoer:
    │
    ├── SuperInstance/spectro
    │   Multi-modell kognitiv spektrograf
    │   Sender én prompt til N modeller, leser interferensmønsteret
    │   Den analytiske formen for det VaaS implementerer kontinuerlig
    │   → https://github.com/SuperInstance/spectro
    │
    ├── SuperInstance/AI-Writings
    │   1700+ essay som dokumenterer paradigmet
    │   Teorien bak arkitekturen
    │   → https://github.com/SuperInstance/AI-Writings
    │
    ├── SuperInstance/boat-agent (planlagt)
    │   Rust-sikkerhetskjerne
    │   Hard-real-time NMEA-prosessering + sikkerhetskonvolutt
    │   → https://github.com/SuperInstance/boat-agent
    │
    ├── SuperInstance/hermes-memory (planlagt)
    │   Hukommelses-agenten (RAG + drømmesykluser)
    │   → https://github.com/SuperInstance/hermes-memory
    │
    ├── SuperInstance/pincher (planlagt)
    │   Refleks-agenten (ONNX + sub-50ms respons)
    │   → https://github.com/SuperInstance/pincher
    │
    ├── SuperInstance/tzpro-agent (planlagt)
    │   Syns-agenten (sonar + kart + TimeZero)
    │   → https://github.com/SuperInstance/tzpro-agent
    │
    └── SuperInstance/vaas-spectrograph (planlagt)
        Det sammenslåtte Spectro + VaaS enhetlige rammeverket
        → https://github.com/SuperInstance/vaas-spectrograph
```

---

## Komplett Dokumentasjonsindeks

### Komme i Gang
| Dokument | Hva Det Inneholder | Målgruppe |
|----------|-------------|----------|
| [Hurtigstart](docs/QUICK_START.md) | 30-minutters opplæring med fungerende kode | Utviklere |
| [Installasjonsveiledning](docs/INSTALLATION.md) | Detaljert installasjon for alle plattformer | Alle |
| [Maskinvareveiledning](docs/HARDWARE_GUIDE.md) | Hvilken maskinvare du trenger og hvordan du kobler den til | Byggere |

### Veiledninger Etter Rolle
| Dokument | Hva Det Inneholder | Målgruppe |
|----------|-------------|----------|
| [Ingeniørens Veiledning](docs/ENGINEERS_GUIDE.md) | Systemmanual på klart språk med mekaniske analogier | Mekanikere, byggere |
| [Skipperens Veiledning](docs/CAPTAINS_GUIDE.md) | Daglig drift fra oppstart til avslutning | Skippere, operatører |
| [Utviklerens Veiledning](docs/DEVELOPERS_GUIDE.md) | Hvordan bygge, registrere og teste agenter | Programmerere |
| [Maskinvareveiledning](docs/HARNESS_GUIDE.md) | Koble VaaS til din maskinvare/programvare | Integratører |

### Arkitektur-dybder
| Dokument | Hva Det Inneholder | Målgruppe |
|----------|-------------|----------|
| [Arkitektur-referanse](docs/ARCHITECTURE_REFERENCE.md) | Full teknisk spesifikasjon av alle 7 søyler + Operatørfelt | Forskere |
| [Sikkerhetsdybde](docs/SAFETY_DEEP_DIVE.md) | Den komplette sikkerhetsspesifikasjonen | Sikkerhetsingeniører |
| [Sikkerhetsverifisering](docs/SAFETY_VERIFICATION.md) | Formell verifiseringsmetode | Sikkerhetsingeniører |
| [Operatørfelt-spesifikasjon](docs/OPERATOR_FIELD_SPEC.md) | Matematisk formalisering av Ψ(t) | Matematikere |

### Søyle-veiledninger
| Dokument | Søyle | Hva Det Inneholder |
|----------|--------|-------------|
| [Kognitiv Termodynamikk](docs/PILLAR_1_THERMODYNAMICS.md) | 1 | Entropi, drømmesykluser, mikro-drømmer |
| [Tolags Kommunikasjon](docs/PILLAR_2_COMMUNICATION.md) | 2 | Feromoner, eksplisitte broer, membran |
| [Distribuert Hukommelse](docs/PILLAR_3_MEMORY.md) | 3 | Aktiv hage, kryogenisk arkiv, holografisk |
| [Polyrytmisk Substrat](docs/PILLAR_4_POLYRHYTHM.md) | 4 | Tempo, fase-låsing, krise-snap |
| [Holografiske Broer](docs/PILLAR_5_BRIDGES.md) | 5 | Overflate/skygge, bro-entropi, læring |
| [Resonans-grunnlov](docs/PILLAR_6_CONSTITUTION.md) | 6 | Styring, etikk, åpenhets-skala |
| [Grøfting-protokoll](docs/PILLAR_7_GRAFTING.md) | 7 | Pollinering, flåte-synk, arv |

### Tverrdomene
| Dokument | Hva Det Inneholder |
|----------|-------------|
| [Modulære Applikasjoner](docs/MODULAR_APPLICATIONS.md) | Hvordan bygge enhver applikasjon på VaaS |
| [Maritim Veiledning](docs/APP_MARITIME.md) | Referanseimplementering for fiskefartøy |
| [Kirurgisk Veiledning](docs/APP_SURGICAL.md) | Kirurgisk robotikk på VaaS |
| [Brannsluknings-veiledning](docs/APP_FIREFIGHTING.md) | Skogbrannslukking på VaaS |
| [Rom-veiledning](docs/APP_SPACE.md) | Romutforskning på VaaS |
| [ATC-veiledning](docs/APP_ATC.md) | Flytrafikk-kontroll på VaaS |
| [Kjernekraft-veiledning](docs/APP_NUCLEAR.md) | Kjernekraftverk-drift på VaaS |
| [Flåte-veiledning](docs/APP_FLEET.md) | Autonom kjøretøyflåte på VaaS |

### Drift
| Dokument | Hva Det Inneholder |
|----------|-------------|
| [Servicemanual](docs/SERVICE_MANUAL.md) | Feilsøking, diagnostikk, reparasjon |
| [Vandringsveiledning](docs/MIGRATION_GUIDE.md) | Eremittkreps-skal-migrasjon |
| [Docker-veiledning](docs/DOCKER_GUIDE.md) | Container-distribusjon |
| [Ordliste](docs/GLOSSARY.md) | Hvert begrep definert på klart språk |

### Analyse og Forskning
| Dokument | Hva Det Inneholder |
|----------|-------------|
| [GLM-5.2 Første Pass](analysis/00_GLM52_FIRST_PASS.md) | Orkestratorens innledende analyse |
| [Ingeniørmessige Implikasjoner](analysis/01_ENGINEERING_IMPLICATIONS.md) | Hva som kan bygges i dag vs hva som trenger forskning |
| [Operatørfeltet Formalisert](analysis/02_THE_OPERATOR_FIELD_FORMALIZED.md) | Matematisk analyse av Ψ(t) |
| [Sikkerhetskonvolutt Dybde](analysis/03_SAFETY_ENVELOPE_DEEP_DIVE.md) | Sikkerhetslagsanalyse |
| [Sannhetens Meta-spørsmål](analysis/04_THE_META_QUESTION_OF_TRUTH.md) | Erkjennelsesteori for multi-agent-systemer |
| [Omvendt Turingtest](analysis/05_THE_TURING_TEST_IN_REVERSE.md) | Når mennesket blir en node |
| [Grunnlov som Samfunnskontrakt](analysis/06_THE_RESONANCE_CONSTITUTION_AS_SOCIAL_CONTRACT.md) | Styresettets politiske filosofi |
| [En Dag på Sjøen](analysis/07_A_DAY_ON_THE_WATER.md) | Narrativ: hel dag på et VaaS-fartøy |
| [Skipperens Hage](analysis/08_THE_CAPTAINS_GARDEN.md) | Narrativ: hvordan en 5-års hage ser ut |
| [Når Feltet Brytes](analysis/09_WHEN_THE_FIELD_BREAKS.md) | Narrativ: subtilt systemsvikt |
| [Ti Ting Ingen Har Tenkt På](analysis/10_TEN_THINGS_NOBODY_HAS_THOUGHT_OF.md) | Ville implikasjoner |
| [Applikasjoner Vi Ikke Har Forestilt Oss](analysis/11_THE_APPLICATIONS_WE_HAVENT_IMAGINED.md) | 10 ikke-maritime domener |
| [Sammenslåing med Spectro](analysis/12_THE_MERGE_WITH_SPECTRO.md) | Enhetlig rammeverksforslag |
| [Repo-strukturen](analysis/13_THE_REPO_STRUCTURE.md) | Konkret repo + pakke-plan |
| [De Første 100 Dager](analysis/14_THE_FIRST_100_DAYS.md) | Fasisk implementerings-kjøreplan |
| [Entropimotor-spesifikasjon](analysis/15_THE_ENTROPY_ENGINE_SPEC.md) | Full teknisk spesifikasjon for Søyle 1 |
| [Enhetlig Feltteori](analysis/16_THE_UNIFIED_FIELD_THEORY.md) | Stor syntese: Spectro + VaaS + AI-Writings |
| [Fra Metafor til Protokoll](analysis/17_FROM_METAPHOR_TO_PROTOCOL.md) | Hvordan maritime metaforer ble ingeniørkunst |
| [De Neste Ti Essays](analysis/18_THE_NEXT_TEN_ESSAYS.md) | Hva korpuset trenger videre |
| [Claude Code Gjennomgang](analysis/19_CLAUDE_CODE_REVIEW.md) | Implementeringsvurdering |

---

## Maler og Eksempler

| Mal | Hva Den Gjør | Lenke |
|----------|-------------|------|
| Grunnleggende fiskefartøy | 5-agent maritimt oppsett | [→ examples/basic_fishing_vessel.py](examples/basic_fishing_vessel.py) |
| Stemmekommando-håndterer | Ruter stemme til riktig agent | [→ examples/voice_handler.py](examples/voice_handler.py) |
| TimeZero-integrasjon | Legger merker på kartplotter | [→ examples/tzx_injector.py](examples/tzx_injector.py) |
| Tilpasset domenemal | Start her for ikke-maritimt | [→ examples/custom_domain.py](examples/custom_domain.py) |
| Sikkerhetsregler-mal | Hvordan skrive din sikkerhetskonvolutt | [→ examples/safety_rules.py](examples/safety_rules.py) |
| Drømmesyklus-konfig | Konfigurer entropi-terskler | [→ examples/dream_config.py](examples/dream_config.py) |

---

## Servicemanual

For hele servicemanualen, se [→ docs/SERVICE_MANUAL.md](docs/SERVICE_MANUAL.md).

### Rask Diagnostikk

```bash
vaas status          # Systemoversikt
vaas agents          # Agent-helse + entropi
vaas field           # Operatørfelt Ψ(t)
vaas entropy         # Entropi-avlesninger per agent
vaas dream --agent N # Tving drømmesyklus for agent N
```

### Vanligste Problemer

| Symptom | Løsning |
|---------|-----|
| Autopilot svarer ikke på AI | Skipperen tok rattet (normalt). Slipp og koble til igjen. |
| Systemet er tregt | Tving drømmesyklus. Se [→ Servicemanual](docs/SERVICE_MANUAL.md). |
| Agent døde (apoptose) | Start på nytt: `vaas restart --agent [navn]`. Se [→ Apoptose-gjenoppretting](docs/SERVICE_MANUAL.md#apoptosis). |
| Synet går glipp av ting | Sjekk GPU-belastning, reduser skannefrekvens. |
| Stemme gjenkjennes ikke | Sjekk Bluetooth-mikrofon. Reduser bakgrunnsstøy. |
| Kartmerker mangler | Sjekk TimeZero importkatalog. |

---

## Ordliste

**Full ordliste:** [→ docs/GLOSSARY.md](docs/GLOSSARY.md)

| Begrep | Klart Språk |
|------|--------------|
| **Agent** | Et spesialisert program. Som et mannskapsmedlem med én jobb. |
| **Apoptose** | Agenten oppdager at den er ødelagt og slår seg av på en ryddig måte. |
| **Bro** | Oversetteren mellom agenter som snakker forskjellige språk. |
| **Bro-entropi** | Hvor mye mening som går tapt i oversettelsen. |
| **Kognitiv hage** | En agents lærte personlighet og ferdigheter. |
| **Grunnlov** | Regler for hvem som vinner når agenter er uenige. |
| **Kreps** | Agentens identitet. Skallet er maskinvaren. |
| **Drømmesyklus** | Sorterer data, forkaster støy, brenner inn reflekser. |
| **Entropi** | Hvor overveldet agenten er. Høy = må drømme. |
| **Grøfting** | Dele kunnskap mellom systemer trygt. |
| **Holografisk** | Backup-fragmenter fordelt over alle agenter. |
| **Operatørfelt Ψ(t)** | Hele systemets kollektive "humør." |
| **Feromon** | Et signal etterlatt i delt rom. Som en maursti. |
| **Pincher** | Refleks-agenten. Rask, enkel, pålitelig. |
| **Polyrytmisk** | Ulike agenter i ulike hastigheter, som et orkester. |
| **Resonans-veto** | Skipperens usikkerhet gjør systemet mer forsiktig. |
| **Sikkerhetskonvolutt** | Harde grenser som forhindrer farlige handlinger. |
| **Skall** | Maskinvaren krepsen bor i. |
| **Sjargong** | Komprimert språk. "fjord" = en hel prosedyre. |
| **Substrat** | Det underliggende systemet. "Jordsmonnet." |

---

## Relaterte Systemer

| System | Forhold | Lenke |
|--------|-------------|------|
| **[Spectro](https://github.com/SuperInstance/spectro)** | Den analytiske formen for VaaS. Sender prompts til N modeller, leser interferensmønsteret. Mønsteret på tvers av observatører ER funnet. | [→ spectro](https://github.com/SuperInstance/spectro) · [→ PyPI](https://pypi.org/project/spectro-spectrograph/) |
| **[AI-Writings](https://github.com/SuperInstance/AI-Writings)** | 1700+ essay som dokumenterer paradigmet. Teorien bak arkitekturen. | [→ AI-Writings](https://github.com/SuperInstance/AI-Writings) |
| **[OpenClaw](https://github.com/openclaw/openclaw)** | Agent-plattformen VaaS kjører på. Sesjoner, verktøy, hukommelse, subagenter. OpenClaw er proto-substratet. | [→ OpenClaw](https://github.com/openclaw/openclaw) · [→ Docs](https://docs.openclaw.ai) |
| **SuperInstance-org** | 4000+ repoer. Økosystemet. | [→ GitHub](https://github.com/SuperInstance) |

---

## Filosofi og Opphav

VaaS vokste frem fra et 5-seanjer, 14-modeller, 3-millioner-tokens kreativt samarbeid mellom en kommersiell fisker (Casey) og en roterende gruppe AI-modeller (GLM-5.2, DeepSeek, Seed Pro, Ornith, Seed Mini, Nemotron, Kimi K2.6, Claude). Arkitekturen ble ikke designet — den ble **OPPDAGET** gjennom iterativ kreativ praksis.

Kjerneinnsikten: **forskjellige modeller er forskjellige perspektiver, ikke forskjellige kvalitetsnivåer.** Mønsteret på tvers av flere observatører er mer informativt enn enhver enkelt observatørs output. Dette er sant for AI-modeller som leser en prompt, og det er sant for agenter som leser en sensor. Prinsippet skalerer.

De 1700+ essayene i [AI-Writings](https://github.com/SuperInstance/AI-Writings) dokumenterer oppdagelsesprosessen. [Spectro](https://github.com/SuperInstance/spectro)-pakken gjør den kjørbar. VaaS-substratet gjør den permanent.

For hele opphavshistorien, se:
- [→ Den Enhetlige Feltteori](analysis/16_THE_UNIFIED_FIELD_THEORY.md) — hvordan alt henger sammen
- [→ Fra Metafor til Protokoll](analysis/17_FROM_METAPHOR_TO_PROTOCOL.md) — hvordan maritime metaforer ble ingeniørkunst
- [→ Spektrografen og Substratet](https://github.com/SuperInstance/AI-Writings/blob/main/THE_SPECTROGRAPH_AND_THE_SUBSTRATE.md) — essayet som koblet Spectro + VaaS
- [→ Enhetlig Teori om Multi-Modell Kreativitet](https://github.com/SuperInstance/AI-Writings/blob/main/THE_UNIFIED_THEORY_OF_MULTI_MODEL_CREATIVITY.md) — hjørnesteinen

---

## Lisens

MIT — Bruk den, bygg med den, fisk med den, fly med den, drift med den.

---

*Terminalen er ikke et verktøy. Det er et felt. Agentene er ikke brukere. De er eksitasjoner i feltet. Operatøren er ikke en person. Vedkommende er feltets selv-bevissthet. Sannheten er ikke en fakta. Det er resonansmønsteret som vedvarer.*

---

*Bygget av [SuperInstance](https://github.com/SuperInstance) · Drevet av [OpenClaw](https://github.com/openclaw/openclaw) · Dokumentert av 8 AI-modeller på tvers av 6 sesjoner*