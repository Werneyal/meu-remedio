# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Single-file PWA for daily medication reminders. No build system, no dependencies, no package.json. The entire app lives in `index.html` with embedded CSS and JS.

## Deployment

```bash
# Deploy to Vercel (production)
npx vercel --prod --yes --name meu-remedio

# Push to GitHub
git push origin main
```

Live URL: https://meu-remedio-sage.vercel.app
GitHub: https://github.com/Werneyal/meu-remedio

## Architecture

All logic is in `index.html` — no modules, no bundler. Key sections within the `<script>` tag:

- **Data layer** — `carregarRemedios()`, `carregarHistorico()`, `carregarConfig()` read/write to `localStorage`. No backend.
- **Data model** — `remedios` array (id, nome, horario, recorrencia, diasSemana, intervalo, dataInicio, notas), `historico` keyed by `remedioId → date → {tomou, horario}`.
- **`deveTomar(remedio, dataISO)`** — core scheduling logic: supports `diario`, `dias_semana`, and `intervalo` recurrence types.
- **Render** — `renderTudo()` calls `renderDataHoje()`, `renderRemedios()`, `renderCalendario()`. All rendering is via `innerHTML`.
- **Notifications** — `agendarLembretes()` uses `setTimeout` per medication (not Service Worker push). Only works while the page is open.

## PWA Files

- `manifest.json` — references `icon-192.png` / `icon-512.png` with `purpose: any` and `maskable`
- `sw.js` — cache-first strategy, cache version is `meu-remedio-v2`. **Bump the version string** when deploying changes that must invalidate the cache.
- Icons are PNG (generated from SVG via `npx sharp-cli`). Do not use SVG with emoji `<text>` — Android ignores them as PWA icons.

## Regenerating Icons

If icons need to be updated:

```bash
npx sharp-cli -i icon-192.svg -o icon-192.png -- resize 192 192
npx sharp-cli -i icon-512.svg -o icon-512.png -- resize 512 512
```
