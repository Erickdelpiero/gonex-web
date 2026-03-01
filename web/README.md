# GONEX Web — Landing Page Corporativa

> Sitio web oficial de **GONEX S.A.C.** — Consultoría tecnológica especializada en Inteligencia Artificial, Energía Renovable e Ingeniería de Software para la industria peruana.

**URL de producción (staging):** `gonex-web.netlify.app`  
**URL definitiva (pendiente RUC):** `gonex.pe`  
**Estado actual:** v0.2 — en revisión activa

---

## Tabla de contenidos

- [Descripción](#descripción)
- [Stack tecnológico](#stack-tecnológico)
- [Estructura del proyecto](#estructura-del-proyecto)
- [Desarrollo local](#desarrollo-local)
- [Deploy](#deploy)
- [Arquitectura de componentes](#arquitectura-de-componentes)
- [Paleta y tipografía](#paleta-y-tipografía)
- [Roadmap](#roadmap)
- [Decisiones técnicas](#decisiones-técnicas)

---

## Descripción

Landing page corporativa de GONEX S.A.C., empresa de consultoría tecnológica con sede en Lima, Perú. El sitio está diseñado para captar leads B2B en los sectores de energía, minería e industria, comunicando credibilidad técnica tanto al ingeniero de campo como al gerente que aprueba el presupuesto.

**Secciones:**
- Hero con gráfico animado y banda de métricas reales
- Servicios (6 áreas de especialización)
- Portafolio (3 proyectos ejecutados con descripciones genéricas por confidencialidad)
- Nosotros — misión, visión, valores y credenciales
- Contacto con formulario y canales directos
- Footer con links institucionales

**Funcionalidades:**
- Modo oscuro / claro con persistencia en `localStorage`
- Botón flotante de WhatsApp Business
- Diseño 100% responsive (móvil, tablet, desktop)
- SEO básico — title tag, meta description, Open Graph

---

## Stack tecnológico

| Capa | Tecnología | Versión | Razón |
|---|---|---|---|
| Framework | Astro | 5.x | Build estático, cero JS en producción, componentes reutilizables |
| Lenguaje | HTML / CSS / JS | — | Sin TypeScript — decisión deliberada para simplicidad |
| Fuentes | Google Fonts | — | Syne (títulos), DM Sans (cuerpo), DM Mono (código/badges) |
| CI/CD | Netlify | — | Deploy automático desde `main` en GitHub |
| Control de versiones | Git + GitHub | — | Repositorio en `github.com/Erickdelpiero/gonex-web` |
| Gestor de Node | fnm | — | Más rápido que nvm, misma funcionalidad |
| Node | v20.20.0 | LTS | Requerido por Astro 5.x |
| npm | 10.8.2 | — | Incluido con Node |

**En producción futura (VPS Contabo):**

| Capa | Tecnología |
|---|---|
| Servidor web | Nginx |
| Contenedores | Docker + Docker Compose |
| SSL | Cloudflare |
| CI/CD | GitHub Actions + SSH |

---

## Estructura del proyecto

```
gonex-web/                          ← Raíz del repositorio
│
├── .gitignore                      ← Excluye node_modules, dist, .env
│
└── web/                            ← Proyecto Astro (carpeta raíz del build)
    │
    ├── public/                     ← Assets estáticos (se copian tal cual al output)
    │   ├── favicon.ico
    │   └── favicon.svg
    │
    ├── src/
    │   ├── components/             ← Componentes reutilizables
    │   │   ├── Navbar.astro        ← Navegación fija + toggle dark/light + hamburguesa
    │   │   ├── Hero.astro          ← Sección principal con gráfico animado
    │   │   ├── Services.astro      ← Grid de 6 servicios
    │   │   ├── Portfolio.astro     ← 3 proyectos ejecutados
    │   │   ├── About.astro         ← Misión, visión, valores, métricas
    │   │   ├── Contact.astro       ← Formulario + canales de contacto
    │   │   ├── Footer.astro        ← Footer con links y copyright
    │   │   └── WhatsAppButton.astro ← Botón flotante WhatsApp
    │   │
    │   ├── layouts/
    │   │   └── BaseLayout.astro    ← Carcasa HTML común (head, meta, fuentes, Navbar)
    │   │
    │   ├── pages/
    │   │   └── index.astro         ← Página principal — importa y compone todos los componentes
    │   │
    │   └── styles/
    │       └── global.css          ← Variables CSS, reset, tipografía, utilidades globales
    │
    ├── astro.config.mjs            ← Configuración de Astro
    ├── package.json                ← Dependencias y scripts
    └── package-lock.json           ← Versiones exactas instaladas (no editar manualmente)
```

---

## Desarrollo local

### Requisitos previos

- Node.js v20 (instalar con `fnm`)
- npm 10+
- Git

### Instalación

```bash
# 1. Clonar el repositorio
git clone https://github.com/Erickdelpiero/gonex-web.git
cd gonex-web/web

# 2. Instalar dependencias
npm install

# 3. Iniciar servidor de desarrollo
npm run dev
```

La web estará disponible en `http://localhost:4321`

El servidor tiene **hot reload** — cada vez que guardas un archivo, el navegador se actualiza automáticamente.

### Comandos disponibles

```bash
npm run dev      # Servidor de desarrollo local (localhost:4321)
npm run build    # Build de producción → genera carpeta dist/
npm run preview  # Vista previa del build de producción en local
```

> **Nota:** `npm run build` siempre reconstruye desde cero. Úsalo para verificar que no hay errores antes de hacer push.

---

## Deploy

### Flujo automático (Netlify)

El deploy es completamente automático:

```
Cambios en VSCode
      ↓
git add . && git commit -m "descripción"
      ↓
git push origin main
      ↓
Netlify detecta el push → ejecuta npm run build → publica dist/
      ↓
Cambios visibles en ~1-2 minutos
```

### Configuración de Netlify

| Campo | Valor |
|---|---|
| Branch | `main` |
| Base directory | `web` |
| Build command | `npm run build` |
| Publish directory | `web/dist` |

### Deploy en VPS propio (futuro — Fase 2)

Cuando llegue el RUC y se compre `gonex.pe`:

1. Contratar Contabo Cloud VPS 10 (4 vCPU / 8GB RAM / 75GB NVMe / $3.96 mes)
2. Instalar Ubuntu 24.04, Nginx, Docker, Certbot
3. Apuntar DNS de `gonex.pe` a Cloudflare → Cloudflare apunta al VPS
4. Configurar GitHub Actions para build + rsync automático al VPS
5. Nginx sirve los archivos estáticos de `dist/`

---

## Arquitectura de componentes

```
index.astro
└── BaseLayout.astro          (head, meta, fuentes, theme script)
    ├── Navbar.astro           (fixed top, dark/light toggle, hamburguesa)
    ├── Hero.astro             (hero section + credentials band)
    ├── Services.astro         (grid 3 cols → 2 cols → 1 col)
    ├── Portfolio.astro        (lista vertical de proyectos)
    ├── About.astro            (2 cols: narrative + sticky mission box)
    ├── Contact.astro          (2 cols: info + formulario)
    ├── Footer.astro           (grid 3 cols + copyright bar)
    └── WhatsAppButton.astro   (fixed bottom-right)
```

**Convención de nombres CSS:** BEM (Block Element Modifier)
```css
.hero {}              /* Block */
.hero__title {}       /* Element */
.hero__card--featured {} /* Modifier */
```

**Sistema de temas:** `data-theme` en el elemento `<html>`
```css
:root, [data-theme="dark"]  { --color-bg: #1D1D1D; }
[data-theme="light"]        { --color-bg: #F4F7F9; }
```
La preferencia se guarda en `localStorage` con la clave `gonex-theme`.

---

## Paleta y tipografía

### Colores — Executive Intelligence

| Variable CSS | Valor | Uso |
|---|---|---|
| `--color-petrol` | `#1B4965` | Azul petróleo — color dominante de marca |
| `--color-charcoal` | `#1D1D1D` | Negro carbón — fondos dark |
| `--color-cyan` | `#00B4D8` | Cyan eléctrico — acento, CTAs, links |
| `--color-pearl` | `#E5E5E5` | Gris perla — texto principal dark mode |
| `--color-offwhite` | `#F8F8F8` | Blanco humo — títulos y fondos light |
| `--color-text-muted` | `#8899A6` | Gris azulado — texto secundario |

### Tipografía

| Variable | Fuente | Uso |
|---|---|---|
| `--font-heading` | Syne (600, 700, 800) | Títulos, logo, CTAs |
| `--font-body` | DM Sans (300, 400, 500, 600) | Todo el cuerpo de texto |
| `--font-mono` | DM Mono (400, 500) | Badges técnicos, labels, código |

---

## Roadmap

### v0 — Estructura base ✅ (actual)
- [x] Entorno local configurado (Node 20, Astro 5, fnm)
- [x] Repositorio GitHub + deploy automático en Netlify
- [x] Navbar con dark/light mode y menú hamburguesa
- [x] Hero con gráfico animado y banda de métricas
- [x] Sección Servicios — 6 servicios en grid responsive
- [x] Sección Portfolio — 3 proyectos ejecutados
- [x] Sección Nosotros — misión, visión, valores
- [x] Formulario de contacto + canales directos
- [x] Footer + botón flotante WhatsApp
- [x] SEO básico (title, meta description, Open Graph)
- [x] Responsive completo (móvil, tablet, desktop)

### v1 — Producción real (pendiente RUC)
- [ ] Compra de dominio `gonex.pe` en NIC.pe
- [ ] Compra de `gonex.com.pe` como protección → redirect a `gonex.pe`
- [ ] Contratación VPS Contabo Cloud VPS 10
- [ ] Configuración Nginx + SSL en VPS
- [ ] Migración de Netlify a VPS propio
- [ ] GitHub Actions para deploy automático al VPS
- [ ] Migración Evolution API + N8N al VPS nuevo
- [ ] Formulario conectado a WhatsApp vía N8N (reemplaza simulación actual)
- [ ] Logo SVG definitivo en navbar y favicon
- [ ] RUC visible en footer
- [ ] Correo `erick@gonex.pe`

### v2 — Expansión de contenido
- [ ] Blog técnico (Astro Content Collections)
- [ ] Página de detalle por servicio
- [ ] Analytics con Umami (self-hosted, sin cookies)
- [ ] Versión en inglés (`/en/`)
- [ ] Demo MDC deployada en `app.gonex.pe/mdc`

### v3 — Ecosistema completo
- [ ] Aula virtual Moodle en `clases.gonex.pe` (pendiente confirmación Héctor)
- [ ] Demos técnicas públicas (OCR, chatbot electoral)
- [ ] Versión en chino (`/zh/`) si se confirma misión China 2026
- [ ] Caso de estudio detallado por proyecto (PDF descargable)

---

## Decisiones técnicas

### ¿Por qué Astro y no React/Next.js?
Astro genera HTML estático puro — Nginx sirve archivos del disco sin procesar nada en el servidor. El consumo de RAM en el VPS es casi cero. Para una landing page de contenido estático, React agrega complejidad innecesaria. Si en el futuro se necesita interactividad compleja, Astro permite agregar componentes React, Vue o Svelte de forma selectiva (islands architecture).

### ¿Por qué no TypeScript?
La curva de aprendizaje de Astro ya es nueva para el proyecto. TypeScript agrega una capa de complejidad que no aporta valor suficiente para un sitio de una sola página sin lógica de negocio compleja. Se puede migrar en v2 si el proyecto escala.

### ¿Por qué Contabo y no Hetzner o DigitalOcean?
Contabo ofrece 4 vCPU / 8GB RAM / 75GB NVMe a $3.96/mes — el mejor ratio precio/recursos disponible. Con 8GB RAM el VPS puede correr simultáneamente Nginx, múltiples contenedores Flask, N8N, Evolution API y eventualmente Moodle sin necesidad de upgrade inmediato.

### ¿Por qué Cloudflare para DNS?
Cloudflare actúa como proxy entre el dominio y el VPS: oculta la IP real del servidor, maneja SSL automáticamente, y ofrece CDN y protección DDoS básica en el plan gratuito. El certificado SSL de Cloudflare elimina la necesidad de configurar Certbot en el VPS para la landing.

### Confidencialidad de clientes en el portfolio
Los proyectos reales (ISA REP vía BAORI, FERCP) se presentan con descripciones genéricas del sector sin nombrar al cliente. Razón: los proyectos fueron ejecutados a través de BAORI como intermediario — ISA REP no tiene relación directa con GONEX aún. Nombrar al cliente sin su conocimiento podría comprometer la relación comercial existente.

---

## Contacto y autoría

**GONEX S.A.C.**  
Lima, Perú  
erick@gonex.pe  
+51 941 915 097  
[linkedin.com/in/erickdelpiero](https://www.linkedin.com/in/erickdelpiero)

**Fundador:** Erick Del Piero  
Ingeniero Mecatrónico — MSc. Inteligencia Artificial — CIP  
1° Puesto NASA Human Exploration Rover Challenge 2023

## 🧞 Commands

All commands are run from the root of the project, from a terminal:

| Command                   | Action                                           |
| :------------------------ | :----------------------------------------------- |
| `npm install`             | Installs dependencies                            |
| `npm run dev`             | Starts local dev server at `localhost:4321`      |
| `npm run build`           | Build your production site to `./dist/`          |
| `npm run preview`         | Preview your build locally, before deploying     |
| `npm run astro ...`       | Run CLI commands like `astro add`, `astro check` |
| `npm run astro -- --help` | Get help using the Astro CLI                     |
