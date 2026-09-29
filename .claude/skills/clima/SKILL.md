---
name: clima
description: Consulta el clima actual o el pronóstico de una ciudad (o de la ubicación actual por IP) usando el servicio gratuito wttr.in, sin necesidad de API key. Úsala cuando el usuario pregunte por el clima, la temperatura, si va a llover, el pronóstico, etc.
---

# Clima

Esta skill obtiene información meteorológica en la terminal usando el servicio público **wttr.in** (no requiere API key ni configuración).

## Cómo usarla

1. Determina la ubicación que pide el usuario:
   - Si menciona una ciudad, usa esa ciudad (en el formato que el usuario la escribió, reemplazando espacios por `+`, ej. `Buenos+Aires`).
   - Si no menciona ninguna ubicación, deja el campo vacío para que wttr.in detecte la ubicación aproximada por IP.

2. Ejecuta la consulta con `curl`, en formato de texto simple (`?format=...` o el reporte compacto):

   ```bash
   # Clima actual, reporte compacto en una línea (rápido, ideal para respuestas breves)
   curl -s "wttr.in/CIUDAD?format=%l:+%C+%t+(sensación+%f),+viento+%w,+humedad+%h"

   # Reporte completo de 3 días (más detallado, ideal si piden "pronóstico")
   curl -s "wttr.in/CIUDAD?M" # M = unidades métricas (°C, km/h)

   # Sin ciudad -> detecta ubicación por IP
   curl -s "wttr.in?format=%l:+%C+%t+(sensación+%f),+viento+%w,+humedad+%h"
   ```

   Notas:
   - Usa siempre `M` o especifica unidades métricas, ya que la interfaz del proyecto está en español y el público es hispanohablante.
   - Si `curl` no está disponible o falla (sin conexión, timeout), informa al usuario que no se pudo obtener el clima y sugiérele revisar su conexión a internet.
   - El reporte completo (`?M`) viene en inglés con arte ASCII; si el usuario solo quiere un dato rápido, prefiere el `format=` compacto y tradúcelo/resúmelo en español al responder.

3. Responde siempre en español, resumiendo los datos relevantes (temperatura, sensación térmica, condición del cielo, viento, humedad, probabilidad de lluvia si está disponible), en línea con el resto del proyecto (README y UI en español).

## Ejemplos

- "¿Qué clima hace en Bogotá?" → `curl -s "wttr.in/Bogota?format=%l:+%C+%t+(sensación+%f),+viento+%w,+humedad+%h"`
- "¿Va a llover hoy?" (sin ciudad) → usar detección por IP y el reporte de 3 días para ver la probabilidad de precipitación.
- "Dame el pronóstico de Madrid para los próximos días" → `curl -s "wttr.in/Madrid?M"` y resumir los 3 días.
