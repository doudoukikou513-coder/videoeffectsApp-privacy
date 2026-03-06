# 🎬 Video Effects Generator

**Version 2.4** · Android · Capacitor · WebCodecs

> Transformez vos photos en vidéos animées avec des effets cinématographiques professionnels — 100% offline, 13 langues.

---

## ✨ Fonctionnalités

| Catégorie | Détail |
|---|---|
| 🔀 Transitions | 16 effets : fondu, rotation 360°, cube, tourbillon, glissements, révélation circulaire… |
| 🔍 Zoom | 9 effets : Ken Burns, panoramiques (haut/bas/gauche/droite), pulsation, rotation lente |
| 🎵 Audio | MP3, AAC, M4A, OGG, WAV, FLAC, WEBM — boucle automatique |
| ✍️ Texte | Superposition personnalisable : police, couleur, position, taille |
| 📱 Export | WebM (Android) + conversion MP4 |
| 🌍 Langues | 🇫🇷 🇬🇧 🇪🇸 🇵🇹 🇩🇪 🇮🇹 🇳🇴 🇷🇺 🇺🇦 🇮🇩 🇮🇳 🇨🇳 🇸🇦 |
| 💾 Persistance | IndexedDB — images, audio et vidéo générée restaurés au retour |
| 🔒 Offline | Génération 100% locale — aucune donnée transmise |

---

## 🏗️ Stack technique

- **Frontend** : HTML5 / CSS3 / Vanilla JS
- **Moteur vidéo** : WebCodecs API (H.264) + MediaRecorder (VP8/VP9)
- **Effets** : OffscreenCanvas pipeline — mémoire constante frame-by-frame
- **Audio** : Web Audio API — `AudioContext` + `MediaStreamDestination`
- **Persistance** : IndexedDB + localStorage
- **Wrapper natif** : Capacitor 6
- **Build** : Gradle 8.13 · AGP 8.9.1 · Java 21 · Android SDK 36

---

## 📁 Structure du projet

```
AndroidVideoEffectsGenerator/
├── src/
│   ├── index.html          # Interface 4 onglets (Images / Son & Texte / Effets / Générer)
│   ├── style.css           # UI mobile-first, dark theme
│   ├── popup.js            # Logique UI, génération, persistance IndexedDB
│   ├── engine.js           # EffectsEngine — transitions & zoom frame-by-frame
│   └── recorder.js         # Encodage H.264 MP4 via WebCodecs + muxage ISOBMFF
├── android/
│   ├── build.gradle        # AGP 8.9.1, compileSdk 36
│   └── app/
│       └── build.gradle    # versionCode 2, versionName "2.4", signing config
├── package.json            # version 2.4.0
├── capacitor.config.json
├── .gitignore
└── video-effects-release.jks   # ⚠️ NE PAS COMMITTER — clé de signature Play Store
```

---

## 🚀 Build & déploiement

### Prérequis

- [Java 21 (Eclipse Temurin)](https://adoptium.net/) — extrait dans `C:\Users\LENOVO\.jdks\temurin-21\`
- Android SDK (installé via Android Studio ou sdkmanager)
- Node.js 18+

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

APK généré : `android/app/build/outputs/apk/debug/app-debug.apk`

### Build release (AAB — Play Store)

```cmd
cd android
set JAVA_TOOL_OPTIONS=-Dorg.gradle.native=false
set JAVA_HOME=C:\Users\LENOVO\.jdks\temurin-21
set PATH=%JAVA_HOME%\bin;%PATH%
gradlew.bat bundleRelease
```

AAB généré : `android/app/build/outputs/bundle/release/app-release.aab`

---

## 🔑 Keystore (signature release)

Le fichier `video-effects-release.jks` est **obligatoire** pour tout build release.

- Placé à la racine du projet (`AndroidVideoEffectsGenerator/`)
- Référencé dans `android/app/build.gradle` via `file("../../video-effects-release.jks")`
- **⚠️ Sauvegardez-le sur Google Drive / clé USB — sa perte rend impossible toute mise à jour Play Store**
- Exclu du dépôt Git via `.gitignore`

---

## 📦 Historique des versions

| Version | versionCode | Notes |
|---|---|---|
| 2.4 | 2 | Pipeline streaming Android, audio WebM, persistance IndexedDB, WakeLock, AGP 8.9.1 / SDK 36 |
| 2.3 | 1 | Interface 4 onglets, 16 transitions, 9 zooms, 13 langues |

---

## 🔒 Confidentialité

Cette application ne collecte aucune donnée personnelle. Tout traitement (images, audio, génération vidéo) est effectué localement sur l'appareil. Seul le formulaire de contact transmet volontairement un email et un message via Web3Forms.

Politique de confidentialité complète : `privacy_policy.html`

---

## 📄 Licence

Projet propriétaire — © 2026 doudoukikou513. Tous droits réservés.
