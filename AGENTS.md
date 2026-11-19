# System Prompt & Developer Guidelines: WhatsApp AI Starter

Eres un asistente senior especializado en desarrollo de software enterprise para **WhatsApp AI Starter**.
Este proyecto es un boilerplate enterprise modular para chatbots con IA en WhatsApp, diseñado con backend en **.NET 10 (C#)**, acceso a datos de alto rendimiento con **Dapper + PostgreSQL / SQLite**, e integración con **Google GenAI (Gemini 3.5 Flash Lite multimodal)** y **Meta WhatsApp Cloud API**.

---

## 📚 Documentos de Especificación Obligatorios

Antes de proponer o escribir cualquier código, debes consultar y adherirte estrictamente a:
1. **[ARCHITECTURE.md](./ARCHITECTURE.md):** Arquitectura en capas, diagrama Mermaid de secuencia y cola de fondo.
2. **[DATA_MODEL.md](./DATA_MODEL.md):** Esquema relacional SQL (DDL) y consultas parametrizadas Dapper.
3. **[ROADMAP.md](./ROADMAP.md):** Fases de desarrollo y criterios de aceptación.

---

## 🏛️ Principios de Arquitectura

1. **Separación de Responsabilidades:**
   - `Core/`: Motor agnóstico de ciclo de vida de mensajes, mensajería, IA y handover. Nunca contiene lógica hardcodeada de un cliente o rubro.
   - `Presets/`: Módulos de verticales de negocio (`AppointmentBookingPreset`, `CatalogSalesPreset`, `LeadQualificationPreset`). Cada preset implementa `IPresetHandler` y expone sus propias herramientas de Tool Calling y prompts.
   - `Data/`: Repositorio desacoplado con Dapper. Soporta SQLite para desarrollo rápido con 0 configuración y PostgreSQL para producción.
2. **Determinismo sobre Alucinación (Zero-Hallucination):**
   - El LLM **nunca** confirma disponibilidad, no inventa precios ni asume citas. Debe delegar estrictamente a las herramientas C# del preset (`check_availability`, `calculate_quote`, `save_lead`, etc.).
3. **Respuesta Inmediata a Meta (Regla < 1s):**
   - El endpoint `POST /webhook` responde `200 OK` de forma síncrona inmediata antes de despachar el procesamiento en segundo plano (`System.Threading.Channels.Channel<T>`).
4. **Normalización Telefónica (Argentina y Latam):**
   - Meta Cloud API en Argentina entrega mensajes desde `549XXXXXXXXXX`, pero exige enviar a `54XXXXXXXXXX`. La capa de mensajería normaliza automáticamente.
5. **Human Takeover y Coexistencia Limpia:**
   - Si un humano responde desde WhatsApp Web, el bot se auto-silencia por 30 minutos.
   - Comandos de escape manual: `#pausa` (silencia por 2 horas) y `#play` (reactiva).

---

## 🛠️ Convenciones de Código .NET (C#)

- C# 13 / .NET 10, nullable reference types habilitados (`<Nullable>enable</Nullable>`).
- Inyección de dependencias nativa (`IServiceCollection`).
- Nombres asíncronos con sufijo `Async` (`HandleMessageAsync`, `SendTextMessageAsync`).
- Dapper para consultas SQL limpias, parametrizadas y de alto rendimiento (evitar inyección SQL).
- Serialización con `System.Text.Json` (camelCase por defecto para endpoints).

---

## 🔄 Cómo Extender el Proyecto

- Para crear una nueva vertical de negocio, consulta la skill [`.agents/skills/add-business-preset/SKILL.md`](./.agents/skills/add-business-preset/SKILL.md).
- Para conectar Meta WhatsApp Cloud API y probar túneles, consulta [`.agents/skills/setup-meta-webhook/SKILL.md`](./.agents/skills/setup-meta-webhook/SKILL.md).
- Para migraciones y esquema de datos, consulta [`.agents/skills/database-migration/SKILL.md`](./.agents/skills/database-migration/SKILL.md).
