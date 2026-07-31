# CLAUDE.md

Guía de contexto para trabajar en este proyecto con Claude Code.

## Qué es esto

Web app (Vite + React) para crear stickers rápido: el usuario arrastra o abre una imagen o video, lo edita en tiempo real (zoom, pan, trim, fps, calidad) y lo exporta a `.webp` de 512x512. Todo corre en el navegador del usuario — **no hay backend**.

## No negociable

Estas son decisiones ya evaluadas en una planeación previa, no improvisadas. No las reconsideres salvo que aparezca un problema real (no una preferencia de estilo) — y si eso pasa, decilo explícitamente antes de cambiarlas, no las reemplaces en silencio:

- **100% client-side.** Ni el archivo original ni el resultado procesado salen del navegador del usuario. Si en algún punto parece necesario algo server-side (analytics, storage, lo que sea), avisá antes de agregarlo — no lo des por sentado.
- **Salida: `.webp`.** 512x512 por default, configurable a resoluciones menores.
- **Imagen → WebP estático** y **video → WebP animado** son dos pipelines distintos con librerías distintas. El detalle de cuáles y por qué está en `.claude/rules/tech-stack.md` — no las cambies sin releer el porqué ahí.
- **Editor de pan/zoom/crop** unificado para imagen y video (una sola UI para las dos ramas). Detalle en el mismo archivo.

## Todo lo demás: tu criterio

Lo de arriba es el "qué" (ya decidido). El "cómo" lo definís vos, sin pedir permiso:

- Estructura de carpetas y componentes, nombres, manejo de estado, estilos, manejo de errores, UX de los controles, cómo mostrar progreso de encoding, testing, lo que sea que no esté en la lista de arriba.
- Si encontrás una librería, patrón o approach mejor para un detalle puntual, usalo — no te limites a lo que está escrito acá si hay una forma más prolija, simple o performante de resolver algo chico. Esto aplica a todo excepto a las decisiones de la sección anterior.
- Ante dos enfoques razonables para un detalle de implementación, elegí uno y seguí. No hace falta consultar cada micro-decisión.

## Comandos

```bash
pnpm install
pnpm dev
pnpm build
pnpm lint
```

Actualizá esta sección apenas el proyecto tenga scripts reales definidos en `package.json` (puede que difieran de esto).

## Reglas adicionales

- `.claude/rules/tech-stack.md` — stack completo, librerías elegidas y el porqué de cada una
- `.claude/rules/media-processing.md` — se carga solo al tocar código de edición/encoding: spec de salida, pipeline de ffmpeg.wasm, cálculo de peso, config de Vite
