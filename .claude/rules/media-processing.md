---
paths:
  - "src/features/editor/**"
  - "src/workers/**"
  - "src/**/*process*"
  - "src/**/*encode*"
  - "vite.config.*"
---

# Procesamiento de media (edición y export)

## Spec de salida

- Formato: `.webp`
- Resolución: 512x512 por default, configurable a valores inferiores (el usuario elige, para bajar peso o subir fps en el resultado)
- Imagen → estático. Video → animado.

## Pipeline de video (referencia, no receta cerrada)

La idea es un solo comando de `ffmpeg.wasm` que hace trim + fps + scale/crop cuadrado + encode a `libwebp` animado. Algo en esta línea — pero confirmá los flags exactos contra la documentación de FFmpeg o el proyecto Video2WebP (referencia pública que hace exactamente esto) antes de darlos por definitivos:

```
-i input -ss {inicio} -to {fin} -vf "fps={fps},scale=512:512:force_original_aspect_ratio=increase,crop=512:512" -loop 0 -an -c:v libwebp -q:v {calidad} -compression_level 6 output.webp
```

## Peso del archivo (real vs. aproximado)

- Peso real: sólo se conoce después de codificar (`blob.size`) — no hay forma de tenerlo antes
- Mientras el usuario mueve sliders: mostrar una estimación heurística (resolución × fps × duración × factor de calidad), sin codificar nada todavía
- Al dejar de tocar el slider (debounce ~300-500ms): disparar una codificación real de baja prioridad en el worker para refinar el número mostrado

## Si el destino final es WhatsApp o Telegram

Son límites de esas plataformas, no del proyecto — mostralos como aviso contra el peso ya calculado, no los hardcodees como bloqueo duro, y confirmá que sigan vigentes si esto termina importando:

- WhatsApp: animado ≤500KB, estático ≤100KB, duración ≤~3s

## Config de Vite si se usa el core multi-thread de ffmpeg.wasm

Sólo hace falta si se suma `@ffmpeg/core-mt` por velocidad. El core single-thread no necesita nada de esto.

```ts
server: {
  headers: {
    'Cross-Origin-Opener-Policy': 'same-origin',
    'Cross-Origin-Embedder-Policy': 'require-corp',
  },
},
```

Replicar estos headers en el servidor de producción, no sólo en el dev server de Vite.
