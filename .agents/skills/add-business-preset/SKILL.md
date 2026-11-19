---
name: add-business-preset
description: Guía paso a paso para crear un nuevo preset comercial en WhatsApp AI Starter (.NET / C#).
---

# Procedimiento: Crear un Nuevo Preset Comercial

Para adaptar el chatbot a una nueva empresa o vertical (por ejemplo, inmobiliaria, restaurante, taller mecánico):

1. **Planificar e implementar el preset en el módulo API (`.NET 10`, ruta objetivo `src/WhatsAppAiStarter.Api/Presets/` una vez iniciada la Fase 2):**
   Implementa la interfaz `IPresetHandler` definida en `ARCHITECTURE.md`:
   ```csharp
   public class MiNuevoRubroPreset : IPresetHandler
   {
       public string Name => "mi-nuevo-rubro";
       public string GetSystemPrompt(BusinessConfig config);
       public IEnumerable<ToolDefinition> GetTools();
       public Task<ToolExecutionResult> ExecuteToolAsync(string toolName, JsonElement args, string senderPhone);
   }
   ```
2. **Definir las herramientas (Function Declarations):**
   - Modela los esquemas JSON de las funciones que Gemini llamará.
   - Aplica lógica determinística estricta (no alucinar precios, consultar base de datos).
3. **Registrar el preset en el contenedor DI (`PresetRegistry` / `Program.cs` en Fase 2):**
   ```csharp
   services.AddSingleton<IPresetHandler, MiNuevoRubroPreset>();
   ```
4. **Configurar el cliente en variables de entorno (`.env`) o configuración de negocio:**
   ```json
   {
     "ActivePreset": "mi-nuevo-rubro",
     "CompanyName": "Empresa XYZ",
     "Tone": "formal y cordial"
   }
   ```
