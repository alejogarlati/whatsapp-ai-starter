# 🚀 WhatsApp AI Starter (.NET 10 + Dapper + PostgreSQL + Gemini Multimodal)

**WhatsApp AI Starter** es un boilerplate enterprise modular para desarrollar y desplegar chatbots comerciales con Inteligencia Artificial en WhatsApp en tiempo récord para cualquier tipo de negocio o empresa.

Diseñado con un backend de alto rendimiento en **.NET 10 (C#)**, acceso a datos ultrarrápido con **Dapper**, compatibilidad dual con **PostgreSQL / SQLite**, visor visual con **Prisma Studio**, integración con **Google GenAI (Gemini 3.5 Flash Lite)** y conexión oficial a **Meta WhatsApp Cloud API**.

---

## 📚 Documentación del Proyecto

El proyecto sigue una metodología de diseño y especificación previa antes de la implementación:

* 🏛️ **[ARCHITECTURE.md](./ARCHITECTURE.md):** Arquitectura completa del sistema, diagramas Mermaid, ciclo de vida del mensaje, capas y pipeline de fondo.
* 🗄️ **[DATA_MODEL.md](./DATA_MODEL.md):** Esquema relacional de base de datos, DDL SQL (PostgreSQL & SQLite), tablas de sesiones, mensajes, leads, citas y catálogo.
* 🗺️ **[ROADMAP.md](./ROADMAP.md):** Plan de desarrollo por fases, hitos, criterios de aceptación y checklist de verificación.
* 🤖 **[AGENTS.md](./AGENTS.md) / [GEMINI.md](./GEMINI.md):** System prompt, reglas de ingeniería de software y directrices para agentes de IA.
* 🛠️ **Skills de Desarrollo (`.agents/skills/`):**
  - [`add-business-preset`](./.agents/skills/add-business-preset/SKILL.md): Guía paso a paso para crear una nueva vertical comercial.
  - [`setup-meta-webhook`](./.agents/skills/setup-meta-webhook/SKILL.md): Conexión con Meta Cloud API y túneles.
  - [`database-migration`](./.agents/skills/database-migration/SKILL.md): Manejo del esquema de datos y Dapper.

---

## 🌟 Características Principales

* **🏛️ Arquitectura Modular por Presets (`IPresetHandler`):**
  Desacopla la lógica comercial del motor de mensajería e IA. Viene preparado con 3 verticales de negocio:
  1. **`appointment-booking` (Turnos y Citas):** Salud, estética, consultorios y profesionales. Consulta disponibilidad y agenda citas automáticamente.
  2. **`catalog-sales` (Catálogo y Ventas):** Corralones, ferreterías, distribuidoras y comercios. Cotizaciones exactas y registro de pedidos.
  3. **`lead-qualification` (Calificación de Leads):** Inmobiliarias, servicios B2B y seguros. Captura presupuesto, zona de interés y notifica al asesor.
* **🧠 Determinismo Estricto (Zero-Hallucination):**
  Gemini nunca calcula precios ni inventa citas a ojo. Delega el 100% de las consultas a herramientas de C# con validación atómica en la base de datos.
* **⚡ Alto Rendimiento con Dapper + PostgreSQL / SQLite:**
  - **SQLite Local (Cero Configuración):** Ideal para desarrollo inmediato sin levantar contenedores ni servidores.
  - **PostgreSQL / Supabase:** Conexión lista para producción cambiando solo la cadena de conexión.
* **🎨 Visor de Datos con Prisma Studio (TypeScript):**
  Incluye esquema Prisma sincronizado para abrir `npx prisma studio` y visualizar/gestionar leads, turnos y órdenes en un panel web instantáneo.
* **📲 Meta WhatsApp Cloud API Oficial:**
  - Handshake de desafío con verificación segura (`GET /webhook`).
  - Respuesta instantánea `200 OK` en <1s (`POST /webhook`).
  - **Normalizador de teléfonos argentinos:** Conversión automática de `549XXXXXXXXXX` a `54XXXXXXXXXX` para la Graph API.
* **🛡️ Coexistencia Humano-Bot y Handover:**
  - Silenciamiento automático temporal cuando un operador responde desde WhatsApp Web.
  - Comandos manuales de escape: `#pausa` (silencia 2 horas) y `#play` (reactiva).
  - Alertas automáticas al WhatsApp del administrador ante turnos o leads confirmados.
* **💬 Simulador Web Local:**
  Página interactiva para probar cualquier preset en el navegador sin gastar cuota de WhatsApp ni requerir túneles.

---

## 📁 Estructura del Repositorio

```text
whatsapp-ai-starter/
├── ARCHITECTURE.md                 # Arquitectura de alto nivel, diagramas y pipeline
├── DATA_MODEL.md                   # Esquema relacional, DDL y contratos Dapper
├── ROADMAP.md                      # Plan de implementación por fases y checklist
├── AGENTS.md / GEMINI.md           # System prompt y directrices de ingeniería
├── .agents/skills/                 # Procedimientos y runbooks automáticos
│   ├── add-business-preset/        # Guía para crear nuevas verticales
│   ├── setup-meta-webhook/         # Guía de conexión con Meta y túneles
│   └── database-migration/         # Gestión de Dapper y esquema SQL
├── .gitignore                      # Exclusiones de build, db y entornos
└── src/ (Planeado en Fase 2)
    └── WhatsAppAiStarter.Api/      # Backend .NET 10 (ASP.NET Core Web API)
```
