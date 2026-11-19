---
name: database-migration
description: Guía de gestión de base de datos con Dapper y PostgreSQL/SQLite en WhatsApp AI Starter.
---

# Procedimiento: Esquema de Datos y Migraciones con Dapper

1. **Estructura de Base de Datos**:
   El proyecto utiliza Dapper para consultas parametrizadas directas. La especificación DDL oficial vive en [`DATA_MODEL.md`](../../../DATA_MODEL.md) (y se implementará en Fase 3 en `Data/Schema.sql`).

2. **Tablas Principales (según `DATA_MODEL.md`)**:
   - `chat_sessions`: Controla el estado del chat (`phone`, `customer_name`, `last_interaction`, `bot_paused_until`, `metadata_json`).
   - `chat_messages`: Historial conversacional (`id`, `session_phone`, `role`, `content`, `whatsapp_message_id`, `created_at`).
   - `leads`: Prospectos calificados (`id`, `session_phone`, `name`, `email`, `interest`, `budget`, `status`, `notes`).
   - `bookings`: Turnos y citas (`id`, `session_phone`, `customer_name`, `service_name`, `booking_date`, `status`, `notes`).
   - `catalog_products`: Productos y precios (`id`, `sku`, `name`, `description`, `price`, `category`, `stock`, `is_active`).
   - `orders` y `order_items`: Solicitudes de compra y comprobantes (`id`, `session_phone`, `total`, `status`, `receipt_url`, `delivery_address`).

3. **Inicialización Automática**:
   En la implementación (Fase 3), `DbInitializer` ejecutará las migraciones DDL al iniciar el backend tanto en SQLite como en PostgreSQL.
