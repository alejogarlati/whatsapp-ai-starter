# 🗺️ Roadmap de Desarrollo: WhatsApp AI Starter

Este documento detalla el plan de trabajo por fases para la implementación paso a paso de **WhatsApp AI Starter**, asegurando que cada etapa sea validada y documentada antes de avanzar a la siguiente.

---

## 🧭 Resumen de Fases

| Fase | Título | Objetivo Principal | Estado |
| :--- | :--- | :--- | :--- |
| **Fase 1** | **Planificación y Arquitectura** | Especificaciones técnicas, diseño de datos, contratos e interfaces. | 🟢 **Completada** |
| **Fase 2** | **Core .NET 10 & Webhook Pipeline** | Endpoint Webhook con ack `<1s`, cola en memoria (`Channel<T>`), normalización. | 🟡 Pendiente |
| **Fase 3** | **Capa de Persistencia (Dapper + Dual DB)** | Repositorio Dapper, soporte SQLite local y PostgreSQL producción. | ⚪ Pendiente |
| **Fase 4** | **Motor de IA (Gemini 3.5 & Tool Calling)** | Cliente Gemini multimodal, orquestador de herramientas, cero alucinación. | ⚪ Pendiente |
| **Fase 5** | **Presets Comerciales (`IPresetHandler`)** | Implementación de Citas, Catálogo y Calificación de Leads. | ⚪ Pendiente |
| **Fase 6** | **Herramientas de Testing y Administración** | Simulador Web local y visor de base de datos con Prisma Studio (TS). | ⚪ Pendiente |
| **Fase 7** | **Documentación de Despliegue y Runbooks** | Dockerfile, docker-compose, Cloudflare Tunnel y guías de producción. | ⚪ Pendiente |

---

## 📌 Detalle de Fases y Criterios de Aceptación

### 🟢 Fase 1: Planificación, Especificaciones y Contratos
- [x] Documento maestro de arquitectura: `ARCHITECTURE.md`.
- [x] Modelo de datos relacional y DDL: `DATA_MODEL.md`.
- [x] Plan de desarrollo e hitos: `ROADMAP.md`.
- [x] System prompt y directrices de desarrollo: `AGENTS.md` y `GEMINI.md`.
- [x] Skills para agentes de desarrollo en `.agents/skills/`.
- **Criterio de Aceptación:** Documentación completa y aprobada por el usuario antes de escribir código ejecutable.

---

### 🟡 Fase 2: Core .NET 10 & Webhook Pipeline
- [ ] Crear solución limpia `.NET 10` Web API (`WhatsAppAiStarter.Api`).
- [ ] Implementar `WebhookController`:
  - `GET /webhook`: Validación segura del desafío de Meta (`hub.challenge`).
  - `POST /webhook`: Validación obligatoria de firma HMAC-SHA256 (`X-Hub-Signature-256`) y respuesta inmediata `200 OK` (< 500ms).
- [ ] Asegurar endpoints `/api/admin/*` y `/api/chat/*` con autenticación/API key, política CORS restringida y renderizado seguro (`textContent`, sin `innerHTML`) en el simulador web.
- [ ] Implementar `System.Threading.Channels.Channel<IncomingMessage>` para desacoplar el procesamiento pesado.
- [ ] Implementar `WhatsAppBackgroundWorker` (`BackgroundService`) consumidor de la cola.
- [ ] Implementar `PhoneNormalizer` para corregir prefijos de Argentina (`549` ➔ `54`).
- [ ] Implementar `HandoverService` para control de silenciamiento por comandos (`#pausa`, `#play`) y operador humano.
- **Criterio de Aceptación:** Test de carga sintético donde el Webhook responde `200 OK` en menos de 500ms y los mensajes llegan al worker en background.

---

### ⚪ Fase 3: Capa de Persistencia con Dapper
- [ ] Implementar `DbConnectionFactory` con soporte dual:
  - SQLite (por defecto, para desarrollo sin levantar contenedores).
  - Npgsql / PostgreSQL (para producción).
- [ ] Implementar `DbInitializer` que ejecute las migraciones DDL (`Schema.sql`) al iniciar la aplicación.
- [ ] Implementar `DapperStorageRepository` con consultas parametrizadas para:
  - Guardar y recuperar sesiones de chat (`chat_sessions`).
  - Guardar y recuperar historial de mensajes (`chat_messages`).
  - Consultar y modificar estado de pausa del bot.
- **Criterio de Aceptación:** Pruebas unitarias/de integración que verifiquen inserción y consulta en SQLite en milisegundos.

---

### ⚪ Fase 4: Motor de IA (Gemini 3.5 Multimodal & Tool Calling)
- [ ] Implementar `GeminiAiEngine` mediante `HttpClient` contra Google Generative Language API.
- [ ] Configurar modelo por defecto: `gemini-3.5-flash-lite`.
- [ ] Soporte para procesamiento multimodal:
  - Descarga y reenvío de audios WhatsApp (mensajes de voz).
  - Descarga e inspección de imágenes (comprobantes de pago o fotos de productos).
- [ ] Orquestador de Tool Calling:
  - Envío de esquemas JSON de herramientas al modelo.
  - Intercepción de `functionCall` y ejecución determinista en C#.
  - Envío de `functionResponse` al modelo para generar la respuesta final al usuario.
- **Criterio de Aceptación:** Diálogo conversacional con Gemini donde ante una pregunta de disponibilidad de turno o precio, el modelo ejecute una Tool en lugar de inventar el dato.

---

### ⚪ Fase 5: Presets Comerciales Modulares
- [ ] Definir contrato `IPresetHandler` en `Core/Interfaces/`.
- [ ] **Preset 1: `AppointmentBookingPreset` (Salud / Estética / Profesionales)**
  - Tools: `check_availability`, `book_appointment`, `cancel_appointment`.
  - Lógica: Consulta atómica en tabla `bookings`, sin doble agendamiento.
- [ ] **Preset 2: `CatalogSalesPreset` (Comercios / Distribuidoras / Ferreterías)**
  - Tools: `search_catalog`, `get_product_price`, `create_order`, `verify_receipt`.
  - Lógica: Búsqueda exacta de productos, cotización cerrada y registro de órdenes.
- [ ] **Preset 3: `LeadQualificationPreset` (Inmobiliarias / Seguros / B2B)**
  - Tools: `qualify_lead`, `record_lead_budget`, `request_agent_contact`.
  - Lógica: Captura guiada de presupuesto, zona y datos de contacto en tabla `leads`.
- [ ] Configuración dinámica en `bot.config.json` para seleccionar el preset activo sin recompilar.
- **Criterio de Aceptación:** Pruebas end-to-end con cada preset activo respondiendo a flujos completos de cliente.

---

### ⚪ Fase 6: Herramientas de Testing y Administración
- [ ] **Simulador Web Local:**
  - Página HTML/JS servida desde `http://localhost:5000/` con interfaz tipo WhatsApp.
  - Permite chatear con el bot simulando diferentes números de teléfono sin requerir Meta Cloud API.
- [ ] **Panel de Administración con Prisma Studio:**
  - Archivo `prisma/schema.prisma` mapeado a la misma base de datos.
  - Script `npm run db:studio` que levanta la interfaz visual para ver leads, turnos y órdenes.
- **Criterio de Aceptación:** Capacidad de probar el bot de punta a punta en el navegador y ver los resultados en Prisma Studio.

---

### ⚪ Fase 7: Despliegue y Producción
- [ ] Dockerfile multi-stage optimizado para ASP.NET Core 10.
- [ ] `docker-compose.yml` con servicio PostgreSQL y Nginx/Cloudflare Tunnel.
- [ ] Runbooks de configuración en Meta Developers y verificación de suscripción WABA.
- **Criterio de Aceptación:** Despliegue en 1 comando con `docker compose up` listo para recibir tráfico de producción.

---

### 🔮 Módulo Futuro / Opcional: RAG Semántico (`PostgreSQL` + `pgvector`)
- [ ] Script de migración opcional para habilitar `CREATE EXTENSION IF NOT EXISTS vector;`.
- [ ] Servicio de indexación de embeddings mediante **Google Gemini `text-embedding-004`** (768 dimensiones).
- [ ] Tool reutilizable `search_knowledge_base` para que presets de catálogos masivos o pólizas consulten contexto difuso sin alucinaciones.
- [ ] Consultas optimizadas con índice HNSW y distancia coseno (`<=>`) ejecutadas mediante Dapper.

