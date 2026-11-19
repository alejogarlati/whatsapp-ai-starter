# Rules & System Instructions: WhatsApp AI Starter

Consulte el archivo principal de directrices y system prompt en [AGENTS.md](./AGENTS.md).
Consulte la arquitectura completa en [ARCHITECTURE.md](./ARCHITECTURE.md), el modelo de datos en [DATA_MODEL.md](./DATA_MODEL.md) y el plan en [ROADMAP.md](./ROADMAP.md).

### Reglas Clave:
1. Mantener el motor agnóstico en `Core/` de la futura solución .NET 10.
2. Encapsular toda lógica comercial en `Presets/` mediante `IPresetHandler`.
3. Consultas a base de datos parametrizadas mediante Dapper (`DapperStorageRepository`), soportando SQLite y PostgreSQL.
4. Respetar siempre el acuse de recibo inmediato `200 OK` en el webhook de WhatsApp desacoplado con `Channel<T>`.
5. Cero alucinación: Gemini delega el 100% de consultas transaccionales a Tools deterministas.
