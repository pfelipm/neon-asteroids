# 🚀 NEON ASTEROIDS

Clásico *Asteroids* con estética **retro-moderna de neón**, generado **100% de forma procedimental** y contenido en un **único archivo `index.html`**. Sin sprites, sin pistas de audio, sin librerías externas a las del propio Three.js — todo (formas, partículas, sonidos y música) se sintetiza en tiempo real.

> Este proyecto se ha creado para poner a prueba a **DeepSeek V4 Flash 0731** — el modelo que impulsa a este asistente. Es una IA excelente para programar y, además, gasta menos que la mota de un jipi: consume una cantidad ridícula de tokens para lo que es capaz de generar. 😄

## 📸 Capturas

Capturas reales del juego (modo WebGL con bloom, realzadas para que el neón resalte):

| | |
|---|---|
| <img src="screenshots/welcome.png" width="480" alt="Menú principal"> | <img src="screenshots/gameplay.png" width="480" alt="Explosión a pantalla completa"> |
| **Menú principal** — asteroides a la deriva y neón | **Explosión** — las partículas llenan la pantalla |
| <img src="screenshots/gameplay_early.png" width="480" alt="Gameplay con asteroides"> | <img src="screenshots/pause.png" width="480" alt="Menú de pausa"> |
| **Gameplay** — ship y asteroides en el espacio | **Menú de pausa** (ESC) con ajustes |

---

## 🕹️ Cómo jugar

| Tecla | Acción |
|---|---|
| `←` / `→` | Rotar la nave |
| `↑` | Propulsión |
| `SPACE` | Disparar |
| `Z` | Hiperespacio (teletransporte de emergencia) |
| `ESC` / `P` | Menú de pausa |
| `M` | Silenciar / activar sonido |
| `R` | Volver al menú (solo en game over / pausa) |
| `ENTER` | Empezar / reiniciar |

**Objetivo:** destruye todos los asteroides del nivel. Cada asteroide grande se divide en dos medianos, y estos en dos pequeños. Cuando el campo está limpio, sube el nivel y la dificultad.

---

## ✨ Características

- **Nave clásica** con propulsión con rozamiento, estela de partículas y llama de empuje animada.
- **Asteroides jagged** (siluetas irregulares y afiladas) generados con *random seeds*: 3 tamaños que se dividen al destruirse.
- **Power-ups** con efecto y duración en HUD:
  - 🛡️ **SHIELD** — escudo temporal, rebota asteroides.
  - 🔫 **RAPID** — cadencia de fuego aumentada.
  - 🎯 **TRIPLE** — disparo en abanico (3 balas).
  - 💥 **BOMB** — limpia la pantalla: todos los asteroides, ovnis y balas enemigas.
- **Ovnis (UFOs)** que aparecen de forma aleatoria con aviso *"⚠ UFO INCOMING ⚠"*, cruzan la pantalla con movimiento sinusoide, **te persiguen y disparan**, tienen vida propia y sueltan un power-up al ser destruidos. Hasta 3 a la vez en niveles altos.
- **Explosiones que llenan la pantalla**: chispas, esquirlas, brasas, ondas de choque expansivas, *screen shake* y destellos de pantalla completa.
- **3 dificultades** ajustables desde el menú (y cambiables en vivo desde la pausa).
- **Puntuación, vidas, niveles, vidas extra** cada 10.000 puntos y **high score persistente** (`localStorage`).
- **Menú AAA** con asteroides a la deriva, estrellas parpadeantes, formas flotantes con animación de *drift + zoom* y título de neón con *flicker*.

---

## 🎵 Audio procedural

Todo el audio se sintetiza con la **Web Audio API** — cero archivos externos:

- **SFX**: disparo, explosiones de asteroide (tono según tamaño), explosión de nave (boom grave + crash), hiperespacio, vida extra, power-up, fuego de ovni, y un chirrido alienígena al aparecer el ovni.
- **Música ambiental** inspirada en *Constance Demby — Novus Magnificat*: un motor evolutivo con capas de **drones** graves (osciladores con *beating*), **pads** de acordes detunados que cambian de escala cada ~11 s, **campanas FM** estocásticas, pulsos graves, LFOs de respiración y **reverb de convolución** sintetizada.
- **Master bus** con compresor/limitador y make-up gain para subir el volumen sin recortar.

---

## 🎨 Técnica: doble renderer

El juego admite **tres modos de render**, elegibles desde el menú (Auto / WebGL / Canvas 2D):

| Renderer | Descripción |
|---|---|
| **WebGL (Three.js)** | Líneas de neón reales construidas con *ribbon geometry* (triángulos con joins en mitra), geometrías procedurales y **UnrealBloomPass** para el glow HDR. |
| **Canvas 2D** | Trazo doble con `shadowBlur` + composición aditiva (`lighter`) para el halo de neón. |
| **Auto** | Intenta WebGL primero y cae a Canvas 2D si el contexto falla. |

Ambos renderers comparten la **misma lógica de juego** (física, colisiones, estado) — solo cambia la capa de dibujo. El badge inferior izquierdo indica el renderer activo, y se puede **cambiar en caliente** desde la pausa (APPLY & RESTART) sin recargar la página.

---

## ⚙️ Detalles técnicos

- **Un solo archivo**: `index.html` (~2.000 líneas) con todo el CSS, HTML y JS embebido.
- **Librerías**: Three.js + addons (`EffectComposer`, `RenderPass`, `UnrealBloomPass`) cargados **dinámicamente** con `import()` vía import map — si eliges Canvas 2D, Three.js ni siquiera se descarga.
- **Formas procedurales**: el *ship*, los asteroides, los power-ups y los ovnis son polígonos generados con `Math.random()` y *seeds* deterministas (el mismo asteroide siempre tiene la misma forma).
- **Colisiones**: por círculos en 2D con *screen wrapping* (el mundo envuelve en los bordes).
- **Física** simplificada de Asteroids: inercia, rozamiento exponencial, velocidad máxima, división de asteroides.
- **Estado**: máquina de estados (menú → jugando → pausa → game over → nivel), transiciones con anuncios y contadores.
- **Dificultad dinámica**: más asteroides por nivel, velocidad +rampa/level, ovnis más rápidos, resistentes y precisos con el tiempo.

---

## 🚀 Cómo ejecutarlo

Al ser un solo archivo, basta con abrirlo en el navegador. Requiere **conexión a internet** la primera vez para cargar Three.js desde el CDN de unpkg (o un caché posterior):

```
xdg-open index.html
```

O sírvelo con cualquier servidor estático:

```
python3 -m http.server 8000
# → http://localhost:8000
```

> ⚠️ Requiere un navegador moderno con WebGL (modo WebGL) o Canvas 2D (modo Canvas). El modo Auto decide por ti.

---

## 🧠 Sobre DeepSeek V4 Flash 0731

Este juego ha sido escrito íntegramente por **DeepSeek V4 Flash 0731** (este asistente), a partir de peticiones en lenguaje natural en español, sin escribir una sola línea a mano.

Puntos fuertes demostrados durante el desarrollo:

- **Código completo y funcional desde el primer intento**, con correcciones rápidas iterando sobre bugs concretos.
- **Refactorizaciones grandes** sin romper el resto: añadir un segundo renderer (Canvas 2D), un motor de audio ambiental y ovnis sobre la marcha.
- **Síntesis de audio y geometría procedural** sin depender de assets externos.
- **Ajuste fino de UX/UI**: menús, hints de teclas, indicadores, estados.
- Y todo ello **consumiendo una cantidad ridículamente baja de tokens** — como se suele decir, gasta menos que la mota de un jipi. 🌿
