# Health Connect Viewer

Google **Health Connect**에 저장된 건강 데이터를 읽어 깔끔한 Material 3 UI로 보여주는 안드로이드 앱입니다. 데이터는 모두 기기 내에서만 처리되며, 외부 서버로 전송되지 않습니다.

## 주요 기능

- **오늘 요약 대시보드**: 걸음 수, 심박수, 수면, 칼로리, 거리, 체중, 혈중 산소, 운동 세션 수
- **상세 차트**: 최근 7일 / 30일 추이 (Vico 차트, 컬럼 / 라인)
- **다국어 UI**: 🇰🇷 한국어 / 🇬🇧 English / 🇯🇵 日本語 (설정 → 언어에서 즉시 전환)
- **테마**: 라이트 / 다크 / 시스템 자동, Android 12+ Dynamic Color 지원
- **권한 흐름**: Health Connect 미설치/권한 미허용 시 안내 화면 + 설치/허용 버튼 제공

## 읽는 데이터 (Health Connect Records)

| 카테고리 | Record 타입 |
|---|---|
| 활동 | StepsRecord, DistanceRecord, ExerciseSessionRecord |
| 에너지 | TotalCaloriesBurnedRecord |
| 심혈관 | HeartRateRecord, OxygenSaturationRecord |
| 수면 | SleepSessionRecord |
| 신체 | WeightRecord |

읽기 전용으로만 접근하며, 사용자가 명시적으로 허용한 권한만 사용합니다.

## 기술 스택

- Kotlin 1.9.24, AGP 8.5.2, Gradle 8.7
- Jetpack Compose + Material 3 (BOM 2024.08)
- `androidx.health.connect:connect-client:1.1.0-alpha07`
- Vico Charts 1.14.0
- minSdk 28, targetSdk 34, compileSdk 34

## 빌드 / 설치

### 사전 준비

1. **Android SDK** (cmdline-tools 또는 Android Studio Hedgehog 이상)
2. **JDK 17**
3. `local.properties` 파일에 SDK 경로 지정:
   ```
   sdk.dir=/home/seokho/Android/Sdk
   ```
   (Manjaro에서 `yay -S android-sdk android-sdk-platform-tools` 또는 Android Studio 사용)

### 디버그 APK 빌드

```bash
cd HealthConnectViewer
./gradlew assembleDebug
# 결과: app/build/outputs/apk/debug/app-debug.apk
```

### 기기에 설치

```bash
adb install -r app/build/outputs/apk/debug/app-debug.apk
```

또는 Android Studio에서 프로젝트를 열고 ▶️ Run.

### Termux에서 빌드 (선택 사항)

```bash
pkg install openjdk-17 git
# Android SDK는 별도 설치 필요 (sdkmanager)
git clone <이 프로젝트 경로> && cd HealthConnectViewer
./gradlew assembleDebug
```

> 참고: Termux에서 풀 빌드는 RAM 4GB+ 권장.

## 기기 사전 설정

1. Android 14 미만이면 **Play 스토어**에서 "Health Connect by Android" 앱 설치 (Android 14+에는 시스템에 내장).
2. Health Connect 설정에서 데이터 소스(예: Google Fit, Samsung Health, Fitbit, Garmin Connect)를 연결.
3. 본 앱 실행 → "권한 허용하기" → 원하는 데이터 종류 체크 후 "허용".

## 프로젝트 구조

```
HealthConnectViewer/
├── app/
│   ├── build.gradle.kts
│   └── src/main/
│       ├── AndroidManifest.xml
│       ├── kotlin/com/example/healthviewer/
│       │   ├── MainActivity.kt              # 진입점, 네비게이션
│       │   ├── HealthViewerApp.kt
│       │   ├── data/
│       │   │   ├── HealthPermissions.kt     # 권한 세트
│       │   │   ├── HealthModels.kt          # 데이터 모델
│       │   │   ├── HealthRepository.kt      # Health Connect I/O
│       │   │   └── SettingsRepository.kt    # 언어/테마 저장
│       │   └── ui/
│       │       ├── theme/                   # Material 3 색/타이포
│       │       ├── i18n/                    # 한/영/일 문자열
│       │       ├── components/              # MetricCard, MetricChart
│       │       ├── screens/                 # Home/Details/Settings/Permission
│       │       └── viewmodel/               # HealthVM, SettingsVM
│       └── res/                             # 아이콘, 테마, strings
├── build.gradle.kts
├── settings.gradle.kts
├── gradle/wrapper/
└── gradlew / gradlew.bat
```

## 언어 전환

앱 하단 **설정 탭 → 언어**에서 한국어 / English / 日本語 칩을 누르면 즉시 전체 UI가 전환됩니다. 선택은 SharedPreferences에 저장되어 다음 실행 시에도 유지됩니다.

내부적으로는 Compose의 `CompositionLocalProvider`로 `LocalStrings`를 주입하는 구조라, 시스템 언어와 독립적으로 동작합니다 (시스템 언어가 영어여도 앱은 한국어로 둘 수 있음).

## 라이선스

이 프로젝트 코드는 자유롭게 수정/사용해도 됩니다. Health Connect SDK는 Google의 Apache 2.0 라이선스, Vico는 Apache 2.0을 따릅니다.
