---
name: setup-meta-webhook
description: Procedimiento para configurar Meta WhatsApp Cloud API y probar Webhooks localmente con Cloudflare Tunnel.
---

# Procedimiento: Configuración del Webhook de WhatsApp en Meta

1. **Obtener Credenciales en Meta Developers**:
   - Entra a `developers.facebook.com` ➔ Tu App ➔ WhatsApp ➔ **Primeros pasos (API Setup)**.
   - Copia `WHATSAPP_TOKEN`, `WHATSAPP_PHONE_NUMBER_ID` y `WHATSAPP_BUSINESS_ACCOUNT_ID`.
2. **Iniciar el Servidor .NET (una vez creada la API en Fase 2)**:
   ```powershell
   dotnet run --project src/WhatsAppAiStarter.Api
   ```
   Asegurar que `WHATSAPP_APP_SECRET` y `WHATSAPP_VERIFY_TOKEN` estén definidos en `.env` para validar la firma `X-Hub-Signature-256`.
3. **Levantar el Túnel HTTPS**:
   ```powershell
   cloudflared tunnel --url http://localhost:5000
   ```
4. **Configurar el Webhook en Meta**:
   - Ve a **WhatsApp ➔ Configuración ➔ Webhook**:
     - **URL de callback:** `https://<tu-subdominio>.trycloudflare.com/webhook`
     - **Verify Token:** El configurado en `WHATSAPP_VERIFY_TOKEN`.
   - Suscríbete al campo: **`messages`**.
5. **Verificar Suscripción de WABA**:
   - Si los mensajes no llegan, llama al endpoint de Graph API:
     ```bash
     POST https://graph.facebook.com/v21.0/{WABA_ID}/subscribed_apps
     Authorization: Bearer {TOKEN}
     ```
