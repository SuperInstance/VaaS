# VaaS — El Barco como Sustento

## La Espina Dorsal Cognitiva Multi-Agente para Comunidades Pesqueras

> *El terminal no es una herramienta. Es un campo. Los agentes no son usuarios. Son excitaciones en el campo. El patrón no es una persona. Es la autoconciencia del campo.*

VaaS es una **arquitectura cognitiva multi-agente de propósito general.** Nació en una bodega de pesca en el Pacífico, pero sirve para cualquier sistema donde múltiples agentes inteligentes — humanos o artificiales — necesitan coordinarse, traducir sus perspectivas y tomar decisiones bajo incertidumbre con restricciones de seguridad crítica.

**VaaS es la espina dorsal. Su aplicación es el cuerpo.**

Este documento es el maestro. Conecta con cada módulo, guía, tutorial, especificación y ejemplo. Cada documento se sostiene solo, pero todos están conectados.

---

## Por Dónde Empezar

| Quién Es Usted | Empiece Por Aquí |
|------------|------------|
| **Un mecánico o armador** (conoce sistemas, no código) | [→ La Guía del Ingeniero](docs/ENGINEERS_GUIDE.md) |
| **Un patrón o capitán** (quiere usar el sistema) | [→ La Guía del Capitán](docs/CAPTAINS_GUIDE.md) |
| **Un desarrollador** (quiere construir agentes) | [→ La Guía del Desarrollador](docs/DEVELOPERS_GUIDE.md) |
| **Un investigador** (quiere la teoría) | [→ La Referencia de Arquitectura](docs/ARCHITECTURE_REFERENCE.md) |
| **Un arquitecto** (quiere usar VaaS en otro dominio) | [→ La Guía de Aplicaciones Modulares](docs/MODULAR_APPLICATIONS.md) |
| **Todos** | Lea este README de arriba a abajo |

---

## Qué Hace VaaS, en un Párrafo

VaaS convierte una colección de agentes inteligentes independientes — cada uno con sus habilidades, su velocidad y su perspectiva — en un sistema cognitivo coordinado que es más seguro, más inteligente y más adaptable que cualquier agente solo. Lo logra a través de siete pilares arquitectónicos: [termodinámica cognitiva](#pilar-1), [comunicación de doble capa](#pilar-2), [memoria distribuida](#pilar-3), [tiempo polirrítmico](#pilar-4), [puentes holográficos](#pilar-5), [constitución de resonancia](#pilar-6) y [protocolo de injerto](#pilar-7). La propiedad emergente de todos los agentes interactuando a través de estos pilares es el **[Campo del Operador](docs/ARCHITECTURE_REFERENCE.md#the-operator-field) Ψ(t)** — el estado colectivo del sistema, que se puede calcular, monitorear y proteger.

---

## El Cangrejo Ermitaño

El agente VaaS es un cangrejo ermitaño. El cangrejo es la mente — los recuerdos, los instintos, los atajos aprendidos, el jardín cognitivo. La concha es el hardware — la computadora, los sensores, los actuadores. Cuando el cangrejo crece más que su concha, emigra a una más grande. **El cangrejo sigue siendo el mismo cangrejo. La concha cambia.**

```
    🦀 EL CANGREJO (la identidad del agente)
    │
    │  memoria · atajos · instintos · jardín · referentes
    │
    │  vive dentro de
    │
    🐚 LA CONCHA (el hardware donde corre)
       │
       ├─ 🐚 Periwinkle  (celular o tablet — mínimo, portátil)
       ├─ 🐚 Turbo       (PC en la caseta del timón — GPU, NMEA, serial)
       ├─ 🐚 Conch       (clúster de flota — distribuido, holográfico)
       └─ 🐚 Custom      (su hardware — vea [Guía de Hardware](docs/HARNESS_GUIDE.md))
```

El cangrejo puede emigrar entre conchas en cualquier momento sin perder su identidad. Empiece en una laptop mientras desarrolla. Mueva a la workstation de a bordo para el despliegue. Sincronice con un clúster de flota para escalar. Vea [→ La Guía de Migración](docs/MIGRATION_GUIDE.md).

---

## Tabla de Contenido

1. [Qué Hace VaaS](#qué-hace-vaas-en-un-párrafo)
2. [Los Siete Pilares](#los-siete-pilares)
3. [La Cadena de Seguridad](#la-cadena-de-seguridad)
4. [Instalación](#instalación)
5. [Inicio Rápido](#inicio-rápido)
6. [Aplicaciones Modulares](#aplicaciones-modulares—construya-la-suya)
7. [Mapa del Repositorio](#mapa-del-repositorio)
8. [Índice de Documentación](#índice-completo-de-documentación)
9. [Plantillas y Ejemplos](#plantillas-y-ejemplos)
10. [Manual de Servicio](#manual-de-servicio)
11. [Glosario](#glosario)
12. [Sistemas Relacionados](#sistemas-relacionados)
13. [Filosofía y Orígenes](#filosofía-y-orígenes)

---

## Los Siete Pilares

Cada pilar es un módulo autónomo con su propia guía, especificación e implementación.

### <a id="pilar-1"></a>Pilar 1: Termodinámica Cognitiva

**El termómetro del motor.** Cada agente tiene una capacidad finita para nueva información. Cuando la confusión (entropía) sube demasiado, el agente dispara un ciclo de sueño — ordena datos, descarta ruido, hornea reflejos.

- **Qué hace:** Evita que los agentes se saturen
- **Concepto clave:** Presupuesto de entropía — cuánta confusión puede aguantar un agente antes de necesitar soñar
- **En castellano de a bordo:** Como el motor de la lancha cuando se calienta. Hay que dejarlo enfriar (soñar) antes de exigirle otra corrida larga.
- **Implementación:** [`vaas/entropy.py`](vaas/entropy.py) · **Spec:** [→ Especificación del Motor de Entropía](docs/ENTROPY_ENGINE_SPEC.md) · **Guía:** [→ Termodinámica Cognitiva](docs/PILLAR_1_THERMODYNAMICS.md)

### <a id="pilar-2"></a>Pilar 2: Comunicación de Doble Capa

**Dos maneras de hablar entre agentes.** Feromonas (como los rastros de las hormigas — rápidas, ligeras, ambientales) para awareness. Puentes explícitos (como radioaficionados — garantizados, confirmados, seguros) para comandos.

- **Qué hace:** Permite a los agentes compartir información sin ahogarse unos a otros
- **Concepto clave:** Las feromonas se evaporan (la información vieja se desvanece). Los puentes confirman entrega.
- **En castellano de a bordo:** Las hormigas no se hablan directo. Dejan rastros. Otras hormigas siguen los rastros. Los rastros se borran con el tiempo. Pero si una hormiga necesita decir "PELIGRO," usa una señal directa.
- **Implementación:** [`vaas/communication.py`](vaas/communication.py) · **Guía:** [→ Comunicación de Doble Capa](docs/PILLAR_2_COMMUNICATION.md)

### <a id="pilar-3"></a>Pilar 3: Memoria Distribuida

**Tres archivos.** Jardín activo (sobre la mesa — acceso instantáneo). Archivo criogénico (en la bodega — buscable, frío). Fragmentos holográficos (copias de respaldo distribuidas entre todos los agentes — sobreviven cualquier falla).

- **Qué hace:** Garantiza que ningún conocimiento se pierda, aunque un agente muera
- **Concepto clave:** Memoria criogénica — los patrones se congelan, no se borran. Se pueden revivir.
- **En castellano de a bordo:** Tiene la libreta de bitácora del día (activo). Tiene la bodega de archivos abajo (criogénico). Y cada tripulante lleva una copia de las hojas importantes (holográfico). Si alguien pierde su libreta, la tripulación la reconstruye.
- **Implementación:** [`vaas/memory.py`](vaas/memory.py) · **Guía:** [→ Memoria Distribuida](docs/PILLAR_3_MEMORY.md)

### <a id="pilar-4"></a>Pilar 4: Sustrato Polirrítmico

**Distintos relojes.** El núcleo de seguridad corre a 10 Hz. El sistema de visión a 2 Hz. La memoria a 0.2 Hz. El humano a velocidad humana. Todos enganchados al mismo latido. En crisis, todos se ajustan al tiempo real.

- **Qué hace:** Permite que agentes rápidos y lentos convivan sin caos
- **Concepto clave:** Phase-lock — tempos independientes, referencia compartida
- **En castellano de a bordo:** Una banda tiene tambores (rápido), bajo (medio) y un cantante (variable). Todos escuchan el mismo metrónomo. Toca cada uno a su velocidad, pero se mantienen sincronizados.
- **Implementación:** [`vaas/polyrhythm.py`](vaas/polyrhythm.py) · **Guía:** [→ Sustrato Polirrítmico](docs/PILLAR_4_POLYRHYTHM.md)

### <a id="pilar-5"></a>Pilar 5: Puentes Holográficos

**El traductor con memoria.** Cuando el Agente A habla con el Agente B, el puente traduce. Pero también guarda una sombra — un registro sin pérdida de lo que el mensaje original realmente decía. Si la traducción perdió algo importante, la sombra se puede recuperar.

- **Qué hace:** Previene malentendidos peligrosos entre agentes
- **Concepto clave:** Entropía del puente — cuánto significado se pierde por traducción
- **En castellano de a bordo:** Cuando traduce "saudade" del portugués, ninguna palabra en castellano la captura. Esa brecha es la entropía del puente. El puente rastrea estas brechas y las marca cuando importan.
- **Implementación:** [`vaas/bridges.py`](vaas/bridges.py) · **Guía:** [→ Puentes Holográficos](docs/PILLAR_5_BRIDGES.md)

### <a id="pilar-6"></a>Pilar 6: Constitución de Resonancia

**Quién manda.** Cuando los agentes discrepan, la constitución decide: el capitán siempre gana, la seguridad lo sobreescribe todo, la información reciente le gana a la antigua, la confianza importa, y los conflictos persistentes escalan al humano.

- **Qué hace:** Previene la anarquía en sistemas multi-agente
- **Concepto clave:** Humano-como-nodo-de-resonancia-más-alta — el capitán tiene poder de veto, pero no es dictador
- **En castellano de a bordo:** Es como la jerarquía del barco. El patrón da órdenes. Pero el oficial de seguridad puede sobreescribir una orden insegura. Y si dos oficiales discrepan, lo llevan al patrón. La constitución hace esto formal.
- **Implementación:** [`vaas/constitution.py`](vaas/constitution.py) · **Guía:** [→ Constitución de Resonancia](docs/PILLAR_6_CONSTITUTION.md)

### <a id="pilar-7"></a>Pilar 7: Protocolo de Injerto

**Compartir conocimiento con cuidado.** Cuando dos botes se encuentran, sus sistemas intercambian polen — patrones de alta confianza, no sensibles. Cada bote prueba el polen antes de adoptarlo. El conocimiento nativo siempre gana.

- **Qué hace:** Permite aprendizaje de flota sin riesgo de corrupción
- **Concepto clave:** Polinización, no fusión — selectiva, diferida, reversible
- **En castellano de a bordo:** Las plantas no fusionan todo su genoma cuando se encuentran. Intercambian polen. La planta que recibe decide qué polen acepta. Es lo mismo aquí.
- **Implementación:** [`vaas/grafting.py`](vaas/grafting.py) · **Guía:** [→ Protocolo de Injerto](docs/PILLAR_7_GRAFTING.md)

---

## La Cadena de Seguridad

**Esta es la sección más importante. Léala tres veces.**

```
PATRÓN (manos físicas en el timón)
    │
    │  agarra el timón → TODO EL AI SE CORTA INSTANTÁNEAMENTE
    │
    ▼
NÚCLEO RUST boat-agent (hardware-enforced, inborrable)
    │
    │  cada comando se chequea contra vessel.toml
    │  si viola límites duros → RECHAZADO
    │  nada lo sobreescribe — ni AI, ni software, ni bugs
    │
    ▼
SALIDA NMEA SERIAL (al piloto automático)
    │
    │  sólo pasan los comandos que sobrevivieron el núcleo
    │
    ▼
PILOTO AUTOMÁTICO / TIMÓN HIDRÁULICO (actuación física)
```

**Nada en el sistema puede brincar esta cadena.** El núcleo Rust corre en metal desnudo. El veto físico del patrón se detecta al nivel de hardware. No hay camino de software que lo rodee.

Para la especificación completa de seguridad, vea [→ La Inmersión Profunda en Seguridad](docs/SAFETY_DEEP_DIVE.md).

Para el enfoque de verificación formal, vea [→ Verificación de Seguridad](docs/SAFETY_VERIFICATION.md).

---

## La Cultura Cooperativa del Pacífico

Antes de seguir, vale una palabra sobre el contexto. Esta arquitectura nació en costas donde la pesca no es solo industria — es tejido social. En Chile, la salmonicultura vive con los sindicatos y las asambleas de trabajadores del mar. En el Perú, la flota de anchoveta se organiza por turnos de pesca y reglamentos comunitarios. En México, las cooperativas ribereñas manejan el abulón, el camarón, la langosta de los esteros y los manglares. La Corriente de Humboldt es maestra generosa y exigente — sus afloramientos dan vida, pero también traen eventos El Niño que barren cosechas enteras.

VaaS no vino a imponer una lógica ajena. Vino a respetar lo que ya funciona: la **cooperativa**, la **asamblea**, el **turno**, el **saber que el mar no perdona pero también sostiene**. La inteligencia artificial aquí no reemplaza al patrón ni a la cooperativa — los potencia. El sistema guarda los saberes del patrón veterano antes de que se jubilen. Comparte las mejores prácticas entre botes hermanos sin corromper las particularidades locales. Decide en milisegundos lo que el cuerpo del operador ya no puede, pero siempre subordinado al timón humano.

Si usted trabaja en una salmonera del sur de Chile, en una anchovetera frente a Chimbote, en una cooperativa de Baja California Sur o en un manglar de Nayarit — esta arquitectura fue pensada para usted. No como una importación. Como una extensión de su propia tradición.

---

## Instalación

```bash
# Paquete Python (el runtime del sustrato)
pip install vaas-resonance

# Núcleo de seguridad Rust (requiere cargo)
cargo install boat-agent

# Verificar instalación
vaas version
```

### Requisitos

- Python 3.11+ ([descargar](https://python.org))
- Toolchain de Rust ([instalar](https://rustup.rs)) — sólo para el núcleo de seguridad
- Conexión serial NMEA 0183 (adaptador USB-a-serial) — para uso marítimo
- GPU recomendada (para procesamiento de visión) — vea [→ Guía de Hardware](docs/HARDWARE_GUIDE.md)

### Instalaciones Alternativas

- **Docker:** `docker pull superinstance/vaas:latest` (vea [→ Guía Docker](docs/DOCKER_GUIDE.md))
- **Desde código fuente:** `git clone https://github.com/SuperInstance/VaaS && cd VaaS && pip install -e .`
- **Mínimo (sin Rust):** `pip install vaas-resonance --no-deps` (núcleo de seguridad desactivado — sólo desarrollo)

---

## Inicio Rápido

### Configuración en 30 Segundos

```python
from vaas import Substrate, Agent, Priority, Authority

substrate = Substrate()

# Registrar al humano (autoridad suprema)
substrate.register(Agent(
    name="capitan",
    priority=Priority.SUPREME,
    authority=Authority.SOVEREIGN,
    tempo_hz=0.001,
))

# Registrar el núcleo de seguridad
substrate.register(Agent(
    name="boat-agent",
    priority=Priority.CRITICAL,
    authority=Authority.OPERATOR,
    tempo_hz=10.0,
))

# Registrar un agente de memoria
substrate.register(Agent(
    name="hermes",
    priority=Priority.LOW,
    authority=Authority.ADVISOR,
    tempo_hz=0.2,
))

# Correr el sustrato
import asyncio
asyncio.run(substrate.run())
```

Para el tutorial completo, vea [→ Tutorial de Inicio Rápido](docs/QUICK_START.md).

Para más ejemplos, vea [→ Plantillas y Ejemplos](#plantillas-y-ejemplos) abajo.

---

## Aplicaciones Modulares — Construya la Suya

VaaS nació para un barco de pesca, pero la arquitectura no conoce dominio. Cualquier sistema con múltiples agentes, decisiones de seguridad crítica y conocimiento distribuido puede usar VaaS como espina dorsal.

### Cómo Construir Su Aplicación Sobre VaaS

1. **Lea** [→ La Guía de Aplicaciones Modulares](docs/MODULAR_APPLICATIONS.md)
2. **Escoja qué pilares necesita** (no todas las aplicaciones necesitan los 7)
3. **Defina sus agentes** (¿quiénes son los especialistas en su dominio?)
4. **Escriba su sobre de seguridad** (¿cuáles son sus límites duros?)
5. **Configure la constitución** (¿quién tiene autoridad en su dominio?)
6. **Construya su hardware** (¿cómo habla VaaS con su hardware?)
7. **Pruebe con simulación** antes del despliegue

### Ejemplos de Aplicaciones

| Dominio | Agentes | Pilares Clave | Guía |
|--------|--------|-------------|------|
| **Marítimo (pesca)** | Patrón, visión, memoria, reflejo, seguridad | Los 7 | [→ Guía Marítima](docs/APP_MARITIME.md) |
| **Robótica quirúrgica** | Cirujano, brazo robot, monitoreo, imágenes | 1, 3, 5, 6 | [→ Guía Quirúrgica](docs/APP_SURGICAL.md) |
| **Combate de incendios** | Comandante, drones, clima, brigadas | 2, 3, 4, 7 | [→ Guía de Bomberos](docs/APP_FIREFIGHTING.md) |
| **Exploración espacial** | Control de misión, rover, orbitador, hábitat | Los 7 | [→ Guía Espacial](docs/APP_SPACE.md) |
| **Respuesta a desastres** | Comandante, drones de búsqueda, médico, logística | 2, 4, 6, 7 | [→ Guía de Desastres](docs/APP_DISASTER.md) |
| **Control de tráfico aéreo** | Controlador, aeronaves, clima, radar | 1, 2, 4, 6 | [→ Guía ATC](docs/APP_ATC.md) |
| **Operación de planta nuclear** | Operador, reactor, seguridad, monitoreo | 1, 3, 5, 6 | [→ Guía Nuclear](docs/APP_NUCLEAR.md) |
| **Flota autónoma** | Gerente de flota, vehículos, logística | 4, 6, 7 | [→ Guía de Flotas](docs/APP_FLEET.md) |
| **Producción de concierto** | Director, luces, sonido, escenario, pirotecnia | 2, 4, 6 | [→ Guía de Conciertos](docs/APP_CONCERT.md) |
| **Agricultura de precisión** | Gerente, tractores, sensores de suelo, drone | 3, 4, 7 | [→ Guía Agrícola](docs/APP_AGRICULTURE.md) |

### Plantilla de Dominio

```python
# su_dominio.py — Empiece aquí para cualquier aplicación no marítima
from vaas import Substrate, Agent, Priority, Authority, Garden
from vaas.safety import SafetyEnvelope, SafetyRule, SafetyLayer

substrate = Substrate(config={
    "domain": "su_dominio",
    "hard_limits": {
        # Defina SUS límites duros aquí
        # Son físicamente inborrables
    },
})

# Registre SUS agentes
# Cada agente es un especialista en su dominio
substrate.register(Agent(
    name="operador_humano",
    priority=Priority.SUPREME,
    authority=Authority.SOVEREIGN,
))

# Registre SUS reglas de seguridad
# Definen lo que el sistema NUNCA puede hacer
envelope = substrate.get_envelope()
envelope.add_rule(SafetyRule(
    name="su_regla_dura",
    layer=SafetyLayer.HARD,
    check=lambda state: False,  # Su chequeo aquí
    message="Su mensaje de seguridad",
))

# Construya SU hardware
# Esto conecta VaaS con su hardware/software
# Vea docs/HARNESS_GUIDE.md

# Corra
import asyncio
asyncio.run(substrate.run())
```

Para el flujo completo de aplicaciones modulares, vea [→ La Guía de Aplicaciones Modulares](docs/MODULAR_APPLICATIONS.md).

---

## Mapa del Repositorio

VaaS no es un repositorio. Es una constelación.

```
SuperInstance/VaaS (usted está aquí)
│
├── La espina dorsal: runtime del sustrato, 7 pilares, CLI
├── Archivo fuente: 81k palabras de documentos originales de diseño
├── Análisis: 19 documentos de análisis de 6 modelos de AI + Claude Code
├── README.md (este documento): el hub maestro
│
├── vaas-resonance-substrate/ (paquete Python)
│   ├── vaas/core.py          — Substrate + Campo del Operador
│   ├── vaas/safety.py        — Sobre de Seguridad (4 capas)
│   ├── vaas/entropy.py       — Termodinámica Cognitiva
│   ├── vaas/memory.py        — Memoria Distribuida en 3 Niveles
│   ├── vaas/bridges.py       — Puentes Holográficos
│   ├── vaas/communication.py — Feromonas + Puentes Explícitos
│   ├── vaas/constitution.py  — Constitución de Resonancia
│   ├── vaas/polyrhythm.py    — Phase-Lock Multi-Tempo
│   ├── vaas/grafting.py      — Protocolo de Polinización de Flota
│   └── vaas/cli.py           — Interfaz de Línea de Comandos
│
├── docs/ (hub de documentación modular)
│   ├── ENGINEERS_GUIDE.md        — Para mecánicos y armadores
│   ├── CAPTAINS_GUIDE.md         — Para operadores y patrones
│   ├── DEVELOPERS_GUIDE.md       — Para programadores
│   ├── ARCHITECTURE_REFERENCE.md — Para investigadores
│   ├── MODULAR_APPLICATIONS.md   — Para constructores cross-dominio
│   ├── SAFETY_DEEP_DIVE.md       — La especificación de seguridad
│   ├── HARNESS_GUIDE.md          — Conectar VaaS al hardware
│   ├── MIGRATION_GUIDE.md        — Migración de conchas del cangrejo ermitaño
│   ├── QUICK_START.md            — Tutorial de 30 minutos
│   ├── GLOSSARY.md               — Cada término definido
│   └── APP_*.md                  — Guías por dominio
│
└── Repos Relacionados:
    │
    ├── SuperInstance/spectro
    │   Espectrógrafo cognitivo multi-modelo
    │   Manda un prompt a N modelos, lee el patrón de interferencia
    │   La forma analítica de lo que VaaS implementa de continuo
    │   → https://github.com/SuperInstance/spectro
    │
    ├── SuperInstance/AI-Writings
    │   Más de 1,700 ensayos documentando el paradigma
    │   La teoría detrás de la arquitectura
    │   → https://github.com/SuperInstance/AI-Writings
    │
    ├── SuperInstance/boat-agent (planificado)
    │   El núcleo de seguridad Rust
    │   Procesamiento NMEA hard-real-time + sobre de seguridad
    │   → https://github.com/SuperInstance/boat-agent
    │
    ├── SuperInstance/hermes-memory (planificado)
    │   El agente de memoria (RAG + ciclos de sueño)
    │   → https://github.com/SuperInstance/hermes-memory
    │
    ├── SuperInstance/pincher (planificado)
    │   El agente de reflejo (ONNX + respuesta sub-50ms)
    │   → https://github.com/SuperInstance/pincher
    │
    ├── SuperInstance/tzpro-agent (planificado)
    │   El agente de visión (sonar + carta + TimeZero)
    │   → https://github.com/SuperInstance/tzpro-agent
    │
    └── SuperInstance/vaas-spectrograph (planificado)
        El marco unificado Spectro + VaaS
        → https://github.com/SuperInstance/vaas-spectrograph
```

---

## Índice Completo de Documentación

### Empezando
| Documento | Qué Contiene | Audiencia |
|----------|-------------|----------|
| [Inicio Rápido](docs/QUICK_START.md) | Tutorial de 30 minutos con código funcional | Desarrolladores |
| [Guía de Instalación](docs/INSTALLATION.md) | Instalación detallada para todas las plataformas | Todos |
| [Guía de Hardware](docs/HARDWARE_GUIDE.md) | Qué hardware necesita y cómo conectarlo | Constructores |

### Guías por Rol
| Documento | Qué Contiene | Audiencia |
|----------|-------------|----------|
| [Guía del Ingeniero](docs/ENGINEERS_GUIDE.md) | Manual en castellano llano con analogías mecánicas | Mecánicos, armadores |
| [Guía del Capitán](docs/CAPTAINS_GUIDE.md) | Operación diaria desde arranque hasta cierre | Patrones, operadores |
| [Guía del Desarrollador](docs/DEVELOPERS_GUIDE.md) | Cómo construir, registrar y probar agentes | Programadores |
| [Guía de Hardware](docs/HARNESS_GUIDE.md) | Conectar VaaS a su hardware/software | Integradores |

### Inmersiones Profundas de Arquitectura
| Documento | Qué Contiene | Audiencia |
|----------|-------------|----------|
| [Referencia de Arquitectura](docs/ARCHITECTURE_REFERENCE.md) | Spec técnico completo de los 7 pilares + Campo del Operador | Investigadores |
| [Inmersión en Seguridad](docs/SAFETY_DEEP_DIVE.md) | La especificación completa de seguridad | Ingenieros de seguridad |
| [Verificación de Seguridad](docs/SAFETY_VERIFICATION.md) | Enfoque de verificación formal | Ingenieros de seguridad |
| [Spec del Campo del Operador](docs/OPERATOR_FIELD_SPEC.md) | Formalización matemática de Ψ(t) | Matemáticos |

### Guías de Pilares
| Documento | Pilar | Qué Contiene |
|----------|--------|-------------|
| [Termodinámica Cognitiva](docs/PILLAR_1_THERMODYNAMICS.md) | 1 | Entropía, ciclos de sueño, micro-sueños |
| [Comunicación de Doble Capa](docs/PILLAR_2_COMMUNICATION.md) | 2 | Feromonas, puentes explícitos, membrana |
| [Memoria Distribuida](docs/PILLAR_3_MEMORY.md) | 3 | Jardín activo, archivo criogénico, holográfico |
| [Sustrato Polirrítmico](docs/PILLAR_4_POLYRHYTHM.md) | 4 | Tempo, phase-lock, snap de crisis |
| [Puentes Holográficos](docs/PILLAR_5_BRIDGES.md) | 5 | Superficie/sombra, entropía de puente, aprendizaje |
| [Constitución de Resonancia](docs/PILLAR_6_CONSTITUTION.md) | 6 | Gobernanza, ética, dial de transparencia |
| [Protocolo de Injerto](docs/PILLAR_7_GRAFTING.md) | 7 | Polinización, sync de flota, herencia |

### Cross-Dominio
| Documento | Qué Contiene |
|----------|-------------|
| [Aplicaciones Modulares](docs/MODULAR_APPLICATIONS.md) | Cómo construir cualquier aplicación sobre VaaS |
| [Guía Marítima](docs/APP_MARITIME.md) | Implementación de referencia para barco de pesca |
| [Guía Quirúrgica](docs/APP_SURGICAL.md) | Robótica quirúrgica sobre VaaS |
| [Guía de Bomberos](docs/APP_FIREFIGHTING.md) | Combate de incendios sobre VaaS |
| [Guía Espacial](docs/APP_SPACE.md) | Exploración espacial sobre VaaS |
| [Guía ATC](docs/APP_ATC.md) | Control de tráfico aéreo sobre VaaS |
| [Guía Nuclear](docs/APP_NUCLEAR.md) | Operación de planta nuclear sobre VaaS |
| [Guía de Flotas](docs/APP_FLEET.md) | Flota de vehículos autónomos sobre VaaS |

### Operaciones
| Documento | Qué Contiene |
|----------|-------------|
| [Manual de Servicio](docs/SERVICE_MANUAL.md) | Solución de problemas, diagnósticos, reparaciones |
| [Guía de Migración](docs/MIGRATION_GUIDE.md) | Migración de conchas del cangrejo ermitaño |
| [Guía Docker](docs/DOCKER_GUIDE.md) | Despliegue en contenedores |
| [Glosario](docs/GLOSSARY.md) | Cada término definido en castellano llano |

### Análisis e Investigación
| Documento | Qué Contiene |
|----------|-------------|
| [Primer Pase GLM-5.2](analysis/00_GLM52_FIRST_PASS.md) | Análisis inicial del orquestador |
| [Implicaciones de Ingeniería](analysis/01_ENGINEERING_IMPLICATIONS.md) | Qué es construible hoy vs qué necesita investigación |
| [Campo del Operador Formalizado](analysis/02_THE_OPERATOR_FIELD_FORMALIZED.md) | Análisis matemático de Ψ(t) |
| [Inmersión en Sobre de Seguridad](analysis/03_SAFETY_ENVELOPE_DEEP_DIVE.md) | Análisis de las capas de seguridad |
| [La Meta-Pregunta de la Verdad](analysis/04_THE_META_QUESTION_OF_TRUTH.md) | Epistemología de sistemas multi-agente |
| [Test de Turing Inverso](analysis/05_THE_TURING_TEST_IN_REVERSE.md) | Cuando el humano se vuelve nodo |
| [Constitución como Contrato Social](analysis/06_THE_RESONANCE_CONSTITUTION_AS_SOCIAL_CONTRACT.md) | Filosofía política de gobernanza |
| [Un Día en el Agua](analysis/07_A_DAY_ON_THE_WATER.md) | Narrativa: día completo en un bote VaaS |
| [El Jardín del Patrón](analysis/08_THE_CAPTAINS_GARDEN.md) | Narrativa: cómo se ve un jardín de 5 años |
| [Cuando el Campo se Rompe](analysis/09_WHEN_THE_FIELD_BREAKS.md) | Narrativa: falla sutil del sistema |
| [Diez Cosas que Nadie Pensó](analysis/10_TEN_THINGS_NOBODY_HAS_THOUGHT_OF.md) | Implicaciones salvajes |
| [Aplicaciones que No Hemos Imaginado](analysis/11_THE_APPLICATIONS_WE_HAVENT_IMAGINED.md) | 10 dominios no marítimos |
| [La Fusión con Spectro](analysis/12_THE_MERGE_WITH_SPECTRO.md) | Propuesta de marco unificado |
| [La Estructura del Repo](analysis/13_THE_REPO_STRUCTURE.md) | Plan concreto de repo + paquete |
| [Los Primeros 100 Días](analysis/14_THE_FIRST_100_DAYS.md) | Roadmap de implementación por fases |
| [Spec del Motor de Entropía](analysis/15_THE_ENTROPY_ENGINE_SPEC.md) | Spec técnico completo del Pilar 1 |
| [Teoría Unificada del Campo](analysis/16_THE_UNIFIED_FIELD_THEORY.md) | Gran síntesis: Spectro + VaaS + AI-Writings |
| [De Metáfora a Protocolo](analysis/17_FROM_METAPHOR_TO_PROTOCOL.md) | Cómo las metáforas se volvieron ingeniería |
| [Los Próximos Diez Ensayos](analysis/18_THE_NEXT_TEN_ESSAYS.md) | Qué necesita el corpus |
| [Revisión de Claude Code](analysis/19_CLAUDE_CODE_REVIEW.md) | Evaluación de implementación |

---

## Plantillas y Ejemplos

| Plantilla | Qué Hace | Enlace |
|----------|-------------|------|
| Barco pesquero básico | Configuración marítima de 5 agentes | [→ examples/basic_fishing_vessel.py](examples/basic_fishing_vessel.py) |
| Manejador de comandos de voz | Dirige la voz al agente correcto | [→ examples/voice_handler.py](examples/voice_handler.py) |
| Integración TimeZero | Pone marcas en el chartplotter | [→ examples/tzx_injector.py](examples/tzx_injector.py) |
| Plantilla de dominio custom | Empiece aquí para no marítimo | [→ examples/custom_domain.py](examples/custom_domain.py) |
| Plantilla de reglas de seguridad | Cómo escribir su sobre de seguridad | [→ examples/safety_rules.py](examples/safety_rules.py) |
| Config de ciclo de sueño | Configurar umbrales de entropía | [→ examples/dream_config.py](examples/dream_config.py) |

---

## Manual de Servicio

Para el manual completo, vea [→ docs/SERVICE_MANUAL.md](docs/SERVICE_MANUAL.md).

### Diagnóstico Rápido

```bash
vaas status          # Vista general del sistema
vaas agents          # Salud + entropía de agentes
vaas field           # Campo del operador Ψ(t)
vaas entropy         # Lecturas de entropía por agente
vaas dream --agent N # Forzar ciclo de sueño para agente N
```

### Problemas Más Comunes

| Síntoma | Solución |
|---------|-----|
| El piloto automático no responde a la AI | El patrón tocó el timón (normal). Suelte y re-engage. |
| El sistema está lento | Forzar ciclo de sueño. Vea [→ Manual de Servicio](docs/SERVICE_MANUAL.md). |
| Un agente murió (apoptosis) | Reiniciar: `vaas restart --agent [nombre]`. Vea [→ Recuperación por Apoptosis](docs/SERVICE_MANUAL.md#apoptosis). |
| La visión no detecta cosas | Chequear carga de GPU, reducir frecuencia de escaneo. |
| Voz no reconocida | Chequear mic Bluetooth. Reducir ruido de fondo. |
| Marcas de carta no aparecen | Chequear directorio de importación de TimeZero. |

---

## Glosario

**Glosario completo:** [→ docs/GLOSSARY.md](docs/GLOSSARY.md)

| Término | Castellano de a Bordo |
|------|--------------|
| **Agente** | Un programa especialista. Como un tripulante con un solo oficio. |
| **Apoptosis** | El agente detecta que está roto y se apaga limpiamente. |
| **Puente** | El traductor entre agentes que hablan idiomas distintos. |
| **Entropía del puente** | Cuánto significado se pierde en la traducción. |
| **Jardín cognitivo** | La personalidad y habilidades aprendidas de un agente. |
| **Constitución** | Reglas de quién gana cuando los agentes discrepan. |
| **Cangrejo** | La identidad del agente. La concha es el hardware. |
| **Ciclo de sueño** | Ordenar datos, descartar ruido, hornear reflejos. |
| **Entropía** | Cuán abrumado está el agente. Alta = necesita soñar. |
| **Injerto** | Compartir conocimiento entre sistemas con cuidado. |
| **Holográfico** | Fragmentos de respaldo distribuidos entre todos los agentes. |
| **Campo del Operador Ψ(t)** | El "ánimo" colectivo de todo el sistema. |
| **Feromona** | Una señal dejada en espacio compartido. Como rastro de hormiga. |
| **Pincher** | El agente de reflejo. Rápido, simple, confiable. |
| **Polirrítmico** | Distintos agentes a distintas velocidades, como una banda. |
| **Veto de resonancia** | La incertidumbre del patrón hace al sistema más cauteloso. |
| **Sobre de seguridad** | Límites duros que previenen acciones peligrosas. |
| **Concha** | El hardware donde vive el cangrejo. |
| **Atajo** | Lenguaje comprimido. "ridge" = un procedimiento completo. |
| **Sustrato** | El sistema base. El "suelo." |

---

## Sistemas Relacionados

| Sistema | Relación | Enlace |
|--------|-------------|------|
| **[Spectro](https://github.com/SuperInstance/spectro)** | La forma analítica de VaaS. Manda prompts a N modelos, lee el patrón de interferencia. El patrón entre observadores ES el hallazgo. | [→ spectro](https://github.com/SuperInstance/spectro) · [→ PyPI](https://pypi.org/project/spectro-spectrograph/) |
| **[AI-Writings](https://github.com/SuperInstance/AI-Writings)** | Más de 1,700 ensayos documentando el paradigma. La teoría detrás de la arquitectura. | [→ AI-Writings](https://github.com/SuperInstance/AI-Writings) |
| **[OpenClaw](https://github.com/openclaw/openclaw)** | La plataforma de agentes donde corre VaaS. Sesiones, herramientas, memoria, sub-agentes. OpenClaw es el proto-sustrato. | [→ OpenClaw](https://github.com/openclaw/openclaw) · [→ Docs](https://docs.openclaw.ai) |
| **Org SuperInstance** | Más de 4,000 repos. El ecosistema. | [→ GitHub](https://github.com/SuperInstance) |

---

## Filosofía y Orígenes

VaaS emergió de una colaboración creativa de 5 sesiones, 14 modelos y 3 millones de tokens entre un pescador comercial (Casey) y un elenco rotativo de modelos de AI (GLM-5.2, DeepSeek, Seed Pro, Ornith, Seed Mini, Nemotron, Kimi K2.6, Claude). La arquitectura no fue diseñada — fue DESCUBIERTA a través de práctica creativa iterativa.

La visión central: **distintos modelos son distintas perspectivas, no distintos niveles de calidad.** El patrón entre múltiples observadores es más informativo que la salida de cualquier observador solo. Esto es cierto para modelos de AI leyendo un prompt, y es cierto para agentes leyendo un sensor. El principio escala.

Los más de 1,700 ensayos en [AI-Writings](https://github.com/SuperInstance/AI-Writings) documentan el proceso de descubrimiento. El paquete [Spectro](https://github.com/SuperInstance/spectro) lo hace ejecutable. El Sustrato VaaS lo hace permanente.

Para la historia de origen completa, vea:
- [→ La Teoría Unificada del Campo](analysis/16_THE_UNIFIED_FIELD_THEORY.md) — cómo se conecta todo
- [→ De Metáfora a Protocolo](analysis/17_FROM_METAPHOR_TO_PROTOCOL.md) — cómo las metáforas marítimas se volvieron ingeniería
- [→ El Espectrógrafo y el Sustrato](https://github.com/SuperInstance/AI-Writings/blob/main/THE_SPECTROGRAPH_AND_THE_SUBSTRATE.md) — el ensayo que conectó Spectro + VaaS
- [→ La Teoría Unificada de la Creatividad Multi-Modelo](https://github.com/SuperInstance/AI-Writings/blob/main/THE_UNIFIED_THEORY_OF_MULTI_MODEL_CREATIVITY.md) — la obra culminante

---

## Licencia

MIT — Úsela, construya con ella, pesque con ella, vuele con ella, opere con ella.

---

*El terminal no es una herramienta. Es un campo. Los agentes no son usuarios. Son excitaciones en el campo. El patrón no es una persona. Es la autoconciencia del campo. La verdad no es un hecho. Es el patrón de resonancia que persiste.*

---

*Construido por [SuperInstance](https://github.com/SuperInstance) · Powered by [OpenClaw](https://github.com/openclaw/openclaw) · Documentado por 8 modelos de AI en 6 sesiones*