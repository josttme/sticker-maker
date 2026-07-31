# Stack y librerías

Decisiones de arquitectura del creador de stickers, con el porqué de cada una. Vienen de investigación previa — no son obvias, pero tampoco son dogma para los detalles chicos dentro de cada una.

## Core

- Vite + React (React 19), TypeScript
- Todo el procesamiento pesado (encoding de imagen o video) corre en Web Worker, nunca bloqueando el hilo principal

## Input y editor

- Drag & drop: File API nativo, o `react-dropzone` si reduce boilerplate sin costo real
- Editor de pan/zoom/crop: **`react-easy-crop`** — soporta imagen y video con la misma API (`<Cropper image={...} />` o `<Cropper video={...} />`), lo que permite un solo componente de editor para las dos ramas en vez de duplicar UI
- Si el editor se queda corto (filtros, rotación avanzada, etc.): **Pintura** (PQINA) es la alternativa, librería paga, también 100% client-side. Evaluarla sólo si `react-easy-crop` realmente no alcanza — no arrancar con ella.

## Imagen → WebP estático

- `canvas.toBlob('image/webp', quality)` nativo alcanza para el caso simple, sin dependencias extra
- **`@jsquash/webp`** (WASM, usa libwebp real) cuando se necesita control fino de calidad/compresión que el canvas nativo no da — correr en su propio worker

## Video → WebP animado

- **`ffmpeg.wasm`** (`@ffmpeg/ffmpeg` + `@ffmpeg/core`) — no existe un encoder de WebP animado nativo del navegador, así que no hay atajo. FFmpeg en WASM decodifica, aplica trim/scale/fps y muxea el WebP animado en un solo comando.
- Arrancar con `@ffmpeg/core` (single-thread) — funciona sin configuración especial de headers
- `@ffmpeg/core-mt` (multi-thread, ~2x más rápido) sólo si hace falta la velocidad extra — requiere `SharedArrayBuffer` y por lo tanto headers COOP/COEP (ver `media-processing.md`)
- Lazy-load el módulo: el core pesa ~31MB, no debe ir en el bundle inicial. Cargarlo recién cuando el usuario entra a editar un video.

## Lo que no está fijado

Estado global (useReducer, Zustand, lo que sea), testing, linting, CI, deploy, diseño visual, estrategia de manejo de errores — nada de esto tiene una decisión tomada todavía. Resolvé con el criterio que tenga más sentido en el momento en que aparezca la necesidad, sin esperar a que esto se actualice primero.
