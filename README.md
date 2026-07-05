# 🎬 Video Effects Generator

**Version 4.0** · Android (Capacitor) + Extension Chrome · WebCodecs

> Transformez vos photos en vidéos animées avec des effets cinématographiques
> professionnels — et donnez-leur vie avec l'IA. 13 langues.

*(English version below — [jump to English](#-english))*

---

## ✨ Fonctionnalités

| Catégorie | Détail |
|---|---|
| 🔀 Transitions | 19 effets : fondu, rotation 360°, cube, tourbillon, glissements, révélation circulaire, stores, damier, split, glitch… |
| 🔍 Zoom | 8 effets : Ken Burns (avant/arrière), panoramiques (4 directions), pulsation, rotation lente |
| 🎨 Filtres | 8 rendus : N&B, sépia, vintage, froid, chaud, grain, vignette |
| 📐 Formats | 7 ratios : 9:16 (TikTok/Reels), 1:1 (Instagram), 16:9 (YouTube), 3:4 |
| 🤖 **IA Vidéo** | **Photo + description → vidéo animée** (cloud). Presets de mouvement, 2 essais offerts, historique local, qualité Éco/Cinéma |
| 🎵 Audio | MP3, AAC, M4A, OGG, WAV, FLAC, WEBM — boucle automatique |
| 🗣️ Narration | Synthèse vocale des textes (aperçu) + incrustation dans la vidéo (Pro, Android) |
| ✍️ Texte | Superposition personnalisable par image : police, couleur, position, taille |
| 🌗 Thème | Mode jour / nuit, avec détection automatique des préférences système |
| 🌍 Langues | 🇫🇷 🇬🇧 🇪🇸 🇵🇹 🇩🇪 🇮🇹 🇳🇴 🇷🇺 🇺🇦 🇮🇩 🇮🇳 🇨🇳 🇸🇦 (RTL pour l'arabe) |
| 💾 Persistance | IndexedDB — images, audio, vidéo générée et historique IA restaurés au retour |
| 📱 Export | WebM (Android) + conversion MP4 |

---

## 💎 Modèle économique (freemium)

| | Gratuit | Pro |
|---|---|---|
| Photos par diaporama | 8 | 30 |
| Transitions / zooms / filtres | de base | tous (dont effets Pro 👑) |
| Narration incrustée | — | ✅ |
| IA Vidéo | 2 essais (filigranés) | filigrane retiré après achat |

- **Déblocage Pro à vie** : via Google Play Billing (app Android) ou Stripe (extension Chrome)
- **Crédits IA** : packs consommables (Starter / Standard / Premium), Google Play ou Stripe
- Identité = **UUID d'installation anonyme** (aucun compte à créer)

---

## 🏗️ Stack technique

**Client (app + extension)**
- Frontend : HTML5 / CSS3 / Vanilla JS
- Moteur vidéo : WebCodecs API (H.264) + MediaRecorder (VP8/VP9)
- Effets : OffscreenCanvas pipeline — mémoire constante frame-by-frame
- Audio : Web Audio API (`AudioContext` + `MediaStreamDestination`)
- Persistance : IndexedDB + localStorage
- Wrapper natif : Capacitor 6 · Plugins Java (Billing, TTSFile)
- Build : Gradle 8.13 · AGP 8.9.1 · Java 21 · Android SDK 36

**Backend IA Vidéo (serverless)**
- Supabase (PostgreSQL + Edge Functions Deno + secrets)
- Génération vidéo : fal.ai (agrégateur — modèles Seedance / Kling)
- Paiements : Google Play Developer API (vérif. serveur) + Stripe (signature vérifiée)

---

## 📁 Structure du projet

```
AndroidVideoEffectsGenerator/
├── src/                         # ⚠️ Client — partagé app + extension
│   ├── index.html               # Interface 5 onglets (Images / Son & Texte / Effets / Générer / IA Vidéo)
│   ├── style.css                # UI mobile-first, thème jour/nuit
│   ├── popup.js                 # UI, génération, persistance, module IA Vidéo
│   ├── engine.js                # EffectsEngine — transitions, zoom, filtres, détection visages
│   ├── recorder.js              # Encodage H.264 MP4 via WebCodecs + muxage ISOBMFF
│   └── tabs.js                  # Navigation des onglets (externalisé pour CSP extension)
├── android/
│   ├── build.gradle             # AGP 8.9.1, compileSdk 36
│   └── app/
│       ├── build.gradle         # versionCode, versionName "4.0", signing config
│       └── src/main/java/.../    # BillingPlugin.java, TTSFilePlugin.java, MainActivity.java
├── supabase/                    # ⚠️ Backend — déployé sur Supabase, PAS sur le client
│   ├── migration_credits.sql    # Tables + RPC atomiques (crédits, anti-rejeu, jobs, signalements)
│   ├── migration_phase2.sql     # Filigrane (ever_purchased)
│   ├── migration_pro_web.sql    # Pro via Stripe (colonne pro + set_pro)
│   └── functions/               # 7 Edge Functions (generate, status, credit, balance, report, stripe-checkout, stripe-webhook)
├── package.json
├── capacitor.config.json
├── .gitignore
└── video-effects-release.jks    # ⚠️ NE PAS COMMITTER — clé de signature Play Store
```

---

## 🚀 Build & déploiement (app Android)

### Prérequis
- [Java 21 (Eclipse Temurin)](https://adoptium.net/) — extrait dans `C:\Users\LENOVO\.jdks\temurin-21\`
- Android SDK · Node.js 18+

### Synchroniser les assets web
```cmd
npx cap sync android
```

### Build debug (APK)
```cmd
cd android
set JAVA_TOOL_OPTIONS=-Dorg.gradle.native=false
set JAVA_HOME=C:\Users\LENOVO\.jdks\temurin-21
set PATH=%JAVA_HOME%\bin;%PATH%
gradlew.bat assembleDebug
```
→ `android/app/build/outputs/apk/debug/app-debug.apk`

### Build release (AAB — Play Store)
```cmd
cd android
set JAVA_TOOL_OPTIONS=-Dorg.gradle.native=false
set JAVA_HOME=C:\Users\LENOVO\.jdks\temurin-21
set PATH=%JAVA_HOME%\bin;%PATH%
gradlew.bat bundleRelease
```
→ `android/app/build/outputs/bundle/release/app-release.aab`

### Backend IA Vidéo (Supabase)
Voir `IA_VIDEO_SETUP.md` et `IA_VIDEO_PHASE2_SETUP.md` : migrations SQL à exécuter,
7 Edge Functions à déployer (⚠️ `stripe-webhook` avec **« Verify JWT » décoché**),
secrets à définir (`FAL_KEY`, `GOOGLE_SERVICE_ACCOUNT`, `STRIPE_SECRET_KEY`…),
puis renseigner `SUPA_URL` + `SUPA_ANON_KEY` dans `popup.js`.

---

## 🔑 Keystore (signature release)

`video-effects-release.jks` est **obligatoire** pour tout build release.
- Placé à la racine, référencé dans `android/app/build.gradle`
- **⚠️ Sauvegardez-le (Drive / USB) — sa perte rend impossible toute mise à jour Play Store**
- Exclu du dépôt via `.gitignore`

---

## 🔒 Confidentialité

Toutes les fonctions de création (diaporamas, effets, transitions, filtres, texte,
narration) s'exécutent **entièrement sur l'appareil, hors ligne**.

**Seule exception : la fonction IA Vidéo**, qui nécessite une connexion. L'image et
le texte saisis sont transmis, via des serveurs sécurisés (Supabase, UE), à un service
tiers de génération (fal.ai), dans le seul but de produire la vidéo. Aucune image ni
prompt n'est conservé durablement ; l'historique est stocké localement. Aucun compte
requis (identifiant d'installation anonyme). Voir `privacy_ia_video.md`.

---

## 📄 Licence

Projet propriétaire — © 2026 doudoukikou513. Tous droits réservés.

---
---

<a name="-english"></a>

# 🎬 Video Effects Generator

**Version 4.0** · Android (Capacitor) + Chrome Extension · WebCodecs

> Turn your photos into animated videos with professional cinematic effects —
> and bring them to life with AI. 13 languages.

---

## ✨ Features

| Category | Details |
|---|---|
| 🔀 Transitions | 19 effects: crossfade, 360° rotation, cube, swirl, slides, circular reveal, blinds, checker, split, glitch… |
| 🔍 Zoom | 8 effects: Ken Burns (in/out), pans (4 directions), pulse, slow rotation |
| 🎨 Filters | 8 looks: B&W, sepia, vintage, cold, warm, grain, vignette |
| 📐 Formats | 7 ratios: 9:16 (TikTok/Reels), 1:1 (Instagram), 16:9 (YouTube), 3:4 |
| 🤖 **AI Video** | **Photo + prompt → animated video** (cloud). Motion presets, 2 free trials, local history, Eco/Cinema quality |
| 🎵 Audio | MP3, AAC, M4A, OGG, WAV, FLAC, WEBM — automatic loop |
| 🗣️ Narration | Text-to-speech of overlays (preview) + baked into the video (Pro, Android) |
| ✍️ Text | Per-image customizable overlay: font, color, position, size |
| 🌗 Theme | Light / dark mode, with automatic system-preference detection |
| 🌍 Languages | 🇫🇷 🇬🇧 🇪🇸 🇵🇹 🇩🇪 🇮🇹 🇳🇴 🇷🇺 🇺🇦 🇮🇩 🇮🇳 🇨🇳 🇸🇦 (RTL for Arabic) |
| 💾 Persistence | IndexedDB — images, audio, generated video and AI history restored on return |
| 📱 Export | WebM (Android) + MP4 conversion |

---

## 💎 Business model (freemium)

| | Free | Pro |
|---|---|---|
| Photos per slideshow | 8 | 30 |
| Transitions / zooms / filters | basic | all (incl. Pro effects 👑) |
| Baked-in narration | — | ✅ |
| AI Video | 2 trials (watermarked) | watermark removed after purchase |

- **Lifetime Pro unlock**: via Google Play Billing (Android app) or Stripe (Chrome extension)
- **AI credits**: consumable packs (Starter / Standard / Premium), Google Play or Stripe
- Identity = **anonymous installation UUID** (no account to create)

---

## 🏗️ Tech stack

**Client (app + extension)**
- Frontend: HTML5 / CSS3 / Vanilla JS
- Video engine: WebCodecs API (H.264) + MediaRecorder (VP8/VP9)
- Effects: OffscreenCanvas pipeline — constant memory, frame-by-frame
- Audio: Web Audio API (`AudioContext` + `MediaStreamDestination`)
- Persistence: IndexedDB + localStorage
- Native wrapper: Capacitor 6 · Java plugins (Billing, TTSFile)
- Build: Gradle 8.13 · AGP 8.9.1 · Java 21 · Android SDK 36

**AI Video backend (serverless)**
- Supabase (PostgreSQL + Deno Edge Functions + secrets)
- Video generation: fal.ai (aggregator — Seedance / Kling models)
- Payments: Google Play Developer API (server-side verification) + Stripe (verified signatures)

---

## 📁 Project structure

```
AndroidVideoEffectsGenerator/
├── src/                         # ⚠️ Client — shared by app + extension
│   ├── index.html               # 5-tab UI (Images / Audio & Text / Effects / Generate / AI Video)
│   ├── style.css                # Mobile-first UI, light/dark theme
│   ├── popup.js                 # UI, generation, persistence, AI Video module
│   ├── engine.js                # EffectsEngine — transitions, zoom, filters, face detection
│   ├── recorder.js              # H.264 MP4 encoding via WebCodecs + ISOBMFF muxing
│   └── tabs.js                  # Tab navigation (externalized for extension CSP)
├── android/
│   ├── build.gradle             # AGP 8.9.1, compileSdk 36
│   └── app/
│       ├── build.gradle         # versionCode, versionName "4.0", signing config
│       └── src/main/java/.../    # BillingPlugin.java, TTSFilePlugin.java, MainActivity.java
├── supabase/                    # ⚠️ Backend — deployed to Supabase, NOT to the client
│   ├── migration_credits.sql    # Tables + atomic RPCs (credits, anti-replay, jobs, reports)
│   ├── migration_phase2.sql     # Watermark (ever_purchased)
│   ├── migration_pro_web.sql    # Pro via Stripe (pro column + set_pro)
│   └── functions/               # 7 Edge Functions (generate, status, credit, balance, report, stripe-checkout, stripe-webhook)
├── package.json
├── capacitor.config.json
├── .gitignore
└── video-effects-release.jks    # ⚠️ DO NOT COMMIT — Play Store signing key
```

---

## 🚀 Build & deploy (Android app)

### Prerequisites
- [Java 21 (Eclipse Temurin)](https://adoptium.net/) — extracted to `C:\Users\LENOVO\.jdks\temurin-21\`
- Android SDK · Node.js 18+

### Sync web assets
```cmd
npx cap sync android
```

### Debug build (APK)
```cmd
cd android
set JAVA_TOOL_OPTIONS=-Dorg.gradle.native=false
set JAVA_HOME=C:\Users\LENOVO\.jdks\temurin-21
set PATH=%JAVA_HOME%\bin;%PATH%
gradlew.bat assembleDebug
```
→ `android/app/build/outputs/apk/debug/app-debug.apk`

### Release build (AAB — Play Store)
```cmd
cd android
set JAVA_TOOL_OPTIONS=-Dorg.gradle.native=false
set JAVA_HOME=C:\Users\LENOVO\.jdks\temurin-21
set PATH=%JAVA_HOME%\bin;%PATH%
gradlew.bat bundleRelease
```
→ `android/app/build/outputs/bundle/release/app-release.aab`

### AI Video backend (Supabase)
See `IA_VIDEO_SETUP.md` and `IA_VIDEO_PHASE2_SETUP.md`: SQL migrations to run,
7 Edge Functions to deploy (⚠️ `stripe-webhook` with **"Verify JWT" unchecked**),
secrets to set (`FAL_KEY`, `GOOGLE_SERVICE_ACCOUNT`, `STRIPE_SECRET_KEY`…),
then fill `SUPA_URL` + `SUPA_ANON_KEY` in `popup.js`.

---

## 🔑 Keystore (release signing)

`video-effects-release.jks` is **required** for any release build.
- Placed at the root, referenced in `android/app/build.gradle`
- **⚠️ Back it up (Drive / USB) — losing it makes Play Store updates impossible**
- Excluded from the repo via `.gitignore`

---

## 🔒 Privacy

All creation features (slideshows, effects, transitions, filters, text, narration)
run **entirely on the device, offline**.

**The only exception is the AI Video feature**, which requires a connection. The
selected image and entered text are sent, through secure servers (Supabase, EU), to
a third-party generation service (fal.ai), solely to produce the video. No image or
prompt is stored permanently; history is kept locally. No account required (anonymous
installation identifier). See `privacy_ia_video.md`.

---

## 📄 License

Proprietary project — © 2026 doudoukikou513. All rights reserved.
