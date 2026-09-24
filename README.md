# 📺 DeshNews 24/7 — Modern Broadcast TV News Android App

<p align="center">
  <img src="app/src/main/res/drawable/app_logo.png" alt="DeshNews Logo" width="120" />
</p>

<p align="center">
  <strong>An authentic, production-grade Android news application inspired by high-contrast Indian TV Studio broadcast aesthetics.</strong><br>
  A personal open-source project engineered with 100% Jetpack Compose (Material 3), Clean Architecture, Jsoup Full-Text Web Extraction, Offline-First Room Caching, and Dagger Hilt.
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Platform-Android-3DDC84?style=for-the-badge&logo=android&logoColor=white" alt="Platform: Android" />
  <img src="https://img.shields.io/badge/Language-Kotlin%202.0-7F52FF?style=for-the-badge&logo=kotlin&logoColor=white" alt="Language: Kotlin" />
  <img src="https://img.shields.io/badge/UI-Jetpack%20Compose%20(M3)-4285F4?style=for-the-badge&logo=jetpackcompose&logoColor=white" alt="Jetpack Compose" />
  <img src="https://img.shields.io/badge/Min%20SDK-26%20(Android%208.0)-00C853?style=for-the-badge" alt="Min SDK: 26" />
  <img src="https://img.shields.io/badge/Target%20SDK-34%20(Android%2014)-00B0FF?style=for-the-badge" alt="Target SDK: 34" />
  <img src="https://img.shields.io/badge/License-MIT-FACC15?style=for-the-badge" alt="License: MIT" />
</p>

---

## 📑 Table of Contents
1. [Overview & Visual Identity](#-overview--visual-identity)
2. [Key Features](#-key-features)
3. [Visual Design System](#-visual-design-system)
4. [Architecture & Engineering Blueprint](#-architecture--engineering-blueprint)
5. [Data Flow & Offline-First Strategy](#-data-flow--offline-first-strategy)
6. [Tech Stack & Dependencies](#-tech-stack--dependencies)
7. [Directory & Project Structure](#-directory--project-structure)
8. [Setup, Build & Execution Guide](#-setup-build--execution-guide)
9. [Author & License](#-author--license)

---

## 🌟 Overview & Visual Identity

**DeshNews 24/7** brings the live television broadcast studio atmosphere directly to mobile news browsing. Designed with a high-contrast dark aesthetic, vibrant crimson breaking alerts, gold accents, edge-to-edge photography, and instant offline accessibility.

### Core Highlights:
- **Broadcast Studio Aesthetic**: Urgent crimson breaking banners, deep navy surfaces, studio gold highlights, and animated live studio status chips.
- **Native Full-Text Article Reader**: Integrated **Jsoup background scraping** extracts full story text directly from publisher web pages, giving readers a clean, distraction-free reading experience without external browser redirects or annoying popups.
- **Offline-First Resilience**: All top headlines, summaries, and images are continuously synchronized with a local **Room SQLite Database**. During connectivity drops, the app seamlessly serves cached stories with an interactive retry banner.

---

## 🚀 Key Features

| Feature | Description |
|---|---|
| 🚨 **Breaking News Flash** | Real-time broadcast tickers with split-pill `DeshNews 24/7` badges. |
| 🎛️ **3D Studio Banner Carousel** | Smooth horizontal carousel showcasing top editorial stories with dynamic width-morphing gold indicators. |
| 📰 **Native In-App Full Story Reader** | Background Jsoup scraper fetches full article paragraphs and displays them in a clean, native typography layout. |
| 📴 **Offline-First Room Caching** | Local SQLite storage through Room Database automatically caches feeds, displaying a smart `OfflineBanner` with retry capabilities during connectivity drops. |
| 🔍 **Real-Time Interactive Search** | Collapsible search bar with instant query execution and dedicated empty-state feedback. |
| 🗂️ **Topic & Category Filtering** | Quick access to World, Politics, Business, Technology, Science, Health, Sports, and Entertainment feeds. |
| 🔖 **Bookmark & Reading List** | One-tap offline bookmarking to save stories for later review. |
| 🌓 **Dynamic Theme Engine** | Instant switching between Studio Dark and Clean Light themes with full system-level contrast compliance. |
| 📤 **Native Android Sharesheet** | Deep-link and story sharing to WhatsApp, Twitter/X, Telegram, and standard communication apps. |

---

## 🎨 Visual Design System

The app utilizes a curated broadcast palette designed for high contrast and visual punch:

| Token | Hex | Role & Visual Context |
|---|---|---|
| **Deep Navy (Canvas)** | `#0A0E17` | Root background canvas, status bar & navigation bar fill |
| **Dark Slate (Ambient)** | `#111827` | Subtle vertical gradient shading and surface depth |
| **Card Surface** | `#151C2C` | Headline cards, bottom navigation containers, and modal sheets |
| **Card Border** | `#1F293D` | 1dp crisp dividers and elevation outlines |
| **Broadcast Red** | `#DC2626` | Breaking news alerts, active tab indicators, and offline banners |
| **Studio Gold** | `#FACC15` | `DeshNews` brand badge, bookmark stars, and active pager dots |
| **Live Green** | `#22C55E` | 🟢 Pulsing `LIVE` broadcast indicators |

### Signature UI Components
- **`BreakingNewsBadge`**: Split horizontal badge fusing black text on Studio Gold (`#FACC15`) with white text on Broadcast Red (`#DC2626`).
- **`StudioBannerCarousel`**: High-impact hero pager highlighting top stories with smooth drag physics and animated indicator transitions.
- **`HeadlineCard`**: High-density headline card featuring Coil-powered image loading, source tag chips, relative timestamps, and bookmark toggling.

---

## 🏛️ Architecture & Engineering Blueprint

DeshNews 24/7 strictly follows **Clean Architecture** principles and the **Unidirectional Data Flow (UDF)** pattern:

```
┌────────────────────────────────────────────────────────────────────────┐
│                        PRESENTATION LAYER                              │
│   • NewsHomeScreen (Feed & Categories)                                 │
│   • NewsDetailScreen (Article Summary & Web Preview)                   │
│   • FullNewsScreen (Native Jsoup Reader)                               │
│   • SettingsBottomSheet (Theme & Version Info)                         │
│                                  ▲                                     │
│                     StateFlow    │    Intents / Events                 │
│                   (NewsUiState)  │   (Search, Refresh, Bookmark)       │
│                                  ▼                                     │
│                            NewsViewModel                               │
└──────────────────────────────────┬─────────────────────────────────────┘
                                   │
                                   ▼
┌────────────────────────────────────────────────────────────────────────┐
│                           DOMAIN LAYER                                 │
│   • NewsArticle (Core Domain Model)                                    │
│   • NewsRepository (Contract Interface)                                │
│   • fetchFullArticleContent() (Business Use Case)                      │
└──────────────────────────────────▲─────────────────────────────────────┘
                                   │
                                   ▼
┌────────────────────────────────────────────────────────────────────────┐
│                            DATA LAYER                                  │
│   • NewsRepositoryImpl (Single Source of Truth Coordinator)            │
│   ├── NewsApiService (Retrofit + Kotlinx Serialization) ──> GNews REST │
│   ├── Jsoup Content Extractor (Background HTML Web Scraper)            │
│   └── NewsDatabase & NewsDao (Room SQLite Local Persistence)           │
└────────────────────────────────────────────────────────────────────────┘
```

---

## 💾 Data Flow & Offline-First Strategy

1. **Immediate Local Cache Emission**: The presentation layer immediately listens to Room DB queries (`ORDER BY cachedAt DESC`). Cached headlines load instantly with zero delay.
2. **Background Network Synchronization**: `NewsRepositoryImpl` fetches fresh JSON payloads from the GNews REST API (`https://gnews.io/api/v4/top-headlines?country=in`), maps DTOs into entities, and updates Room.
3. **Resilient Offline Mode**: When the device loses connection, the UI seamlessly displays the cached articles accompanied by a non-intrusive `OfflineBanner` with a "Tap to Retry" trigger.
4. **Jsoup On-Demand Full Article Scraping**: When reading a story in reader mode, Jsoup fetches the web page in background coroutines, strips out ads/scripts, extracts the article paragraphs, and renders them in native typography.

---

## 🛠️ Tech Stack & Dependencies

| Category | Technology / Library | Purpose |
|---|---|---|
| **Language** | Kotlin 2.0.20 | Modern language with Coroutines & StateFlow |
| **UI Framework** | Jetpack Compose (BOM 2024.02.02) | Declarative UI with Material 3 components |
| **Dependency Injection** | Dagger Hilt 2.51 | Dependency injection across ViewModels & Repositories |
| **Local Database** | Room Database 2.6.1 + KSP | SQLite persistence for offline caching |
| **Networking** | Retrofit 2.9.0 + OkHttp 4.12.0 | Type-safe REST client with HTTP logging interceptor |
| **Serialization** | Kotlinx Serialization 1.6.3 | Fast JSON parsing without reflection overhead |
| **Image Loading** | Coil Compose 2.6.0 | Asynchronous image loading with memory caching |
| **Web Scraping** | Jsoup 1.17.2 | HTML parsing for native in-app article extraction |
| **Navigation** | Navigation Compose 2.7.7 | Single-activity Compose NavHost routing |
| **Custom Tabs** | AndroidX Browser | In-app fallback browser integration |

---

## 📁 Directory & Project Structure

```
Deshnews 24/
├── app/
│   ├── build.gradle.kts                      # Module build script, dependencies & plugins
│   ├── proguard-rules.pro                    # R8 / ProGuard rules for release builds
│   └── src/main/
│       ├── AndroidManifest.xml               # App declarations & network permissions
│       ├── java/com/deshnews/app/
│       │   ├── DeshNewsApplication.kt        # Application class & Hilt Android App root
│       │   ├── MainActivity.kt               # Single Activity hosting Compose NavHost
│       │   ├── data/
│       │   │   ├── local/                    # Room Database, DAO & NewsEntity
│       │   │   ├── remote/                   # Retrofit API Service & DTOs
│       │   │   └── repository/               # NewsRepositoryImpl & Jsoup scraper
│       │   ├── di/                           # Hilt Dagger Modules (Database, Network, Repo)
│       │   ├── domain/                       # NewsArticle model & NewsRepository interface
│       │   └── presentation/
│       │       ├── state/                    # NewsUiState, UiEvent & MVI models
│       │       ├── viewmodel/                # NewsViewModel (StateFlow & Coroutine scopes)
│       │       └── ui/
│       │           ├── components/           # BreakingNewsBadge, HeadlineCard, StudioCarousel
│       │           ├── screen/               # NewsHomeScreen, NewsDetailScreen, FullNewsScreen
│       │           └── theme/                # Color, Typography, Shape & Theme
│       └── res/                              # Drawables, mipmaps, strings & XML themes
├── gradle/libs.versions.toml                 # Centralized Gradle Version Catalog
├── build.gradle.kts                          # Root build script
├── settings.gradle.kts                       # Plugin & repository declarations
├── gradle.properties                         # JVM memory flags & compiler arguments
├── local.properties.example                  # Template for local SDK and API keys
└── README.md                                 # Project documentation
```

---

## 🚀 Setup, Build & Execution Guide

### Prerequisites
- **Android Studio**: Ladybug (2024.2.1) / Iguana (2023.2.1) or later.
- **JDK**: Java 17.
- **Android SDK**: Compile SDK 34, Min SDK 26.
- **Device / Emulator**: Android 8.0 (API 26) or higher.

### Quick Start
1. **Clone the Repository**:
   ```bash
   git clone https://github.com/codedbyamankanojiya/DeshNews.git
   cd DeshNews
   ```

2. **Configure API Key**:
   - Get a free API key at [GNews.io](https://gnews.io).
   - Create or edit `local.properties` in the project root:
     ```properties
     GNEWS_API_KEY=your_actual_gnews_api_key_here
     ```
   *(Note: `local.properties` is git-ignored and never committed).*

3. **Open in Android Studio**:
   - Open Android Studio, select **Open**, and choose the `Deshnews 24` folder.
   - Allow Gradle to sync dependencies.

4. **Build & Run**:
   - Press **`Shift + F10`** (or click the green **Run** button) to launch the app on your emulator or connected device.

5. **Generate Standalone APK**:
   ```bash
   ./gradlew assembleDebug
   ```
   The APK will be generated at:
   `app/build/outputs/apk/debug/app-debug.apk`

---

## 👨‍💻 Author & License

This application is personally developed and maintained by **Aman Kanojiya**:
- **GitHub**: [@codedbyamankanojiya](https://github.com/codedbyamankanojiya)
- **LinkedIn**: [Aman Kanojiya](https://linkedin.com/in/aman-kanojiya-7386822b0)

This project is open-source and licensed under the **MIT License** — feel free to explore, fork, and star the repository!
