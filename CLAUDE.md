# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 專案概述

這是一個 Flutter 插件專案，名為 `platform_metadata`，用於從 Android 的 AndroidManifest.xml 和 iOS 的 info.plist 中讀取元數據值。

## 專案結構

```
platform_metadata/
├── lib/                          # Dart 介面層
│   ├── platform_metadata.dart              # 主要 API 介面
│   ├── platform_metadata_platform_interface.dart  # 平台介面抽象
│   └── platform_metadata_method_channel.dart      # Method Channel 實作
├── android/                      # Android 平台實作
│   └── src/main/kotlin/com/shivammishra/platform_metadata/
│       └── PlatformMetadataPlugin.kt       # Kotlin 平台程式碼
├── ios/                         # iOS 平台實作
│   └── Classes/
│       └── PlatformMetadataPlugin.swift    # Swift 平台程式碼
├── example/                     # 範例應用程式
│   ├── lib/main.dart           # 範例程式碼
│   ├── android/                # Android 範例配置
│   └── ios/                    # iOS 範例配置
└── test/                       # 測試檔案
```

## 常用命令

### 開發和建置
```bash
# 使用 fvm 管理 Flutter 版本
fvm flutter pub get                    # 獲取依賴
fvm flutter pub upgrade               # 升級依賴

# 測試
fvm flutter test                      # 執行所有測試
fvm flutter test test/platform_metadata_test.dart  # 執行特定測試

# 程式碼品質檢查
fvm flutter analyze                   # 執行靜態分析
fvm dart format .                     # 格式化程式碼
fvm dart fix --apply                  # 自動修復問題

# 範例應用程式
cd example
fvm flutter run                       # 執行範例應用程式
fvm flutter build apk               # 建置 Android APK
fvm flutter build ios               # 建置 iOS 應用程式
```

### 插件發布
```bash
fvm flutter pub publish --dry-run    # 預覽發布內容
fvm flutter pub publish              # 發布到 pub.dev
```

## 架構說明

### 核心架構
- **平台介面模式**: 使用 `PlatformInterface` 抽象類別定義統一介面
- **Method Channel**: 透過 `MethodChannel` 與原生平台通訊
- **多平台支援**: 支援 Android、iOS、Linux、macOS、Windows、Web

### 程式碼流程
1. `PlatformMetadata.getMetaDataValue()` - 主要 API 入口
2. `PlatformMetadataPlatform.instance` - 平台介面實例
3. `MethodChannelPlatformMetadata` - Method Channel 實作
4. 原生平台處理:
   - Android: 讀取 ApplicationInfo.metaData
   - iOS: 讀取 Bundle.main.infoDictionary

### 關鍵檔案
- `platform_metadata.dart:5` - 主要 API 方法
- `platform_metadata_method_channel.dart:13-17` - Method Channel 實作
- `PlatformMetadataPlugin.kt:28-41` - Android 原生實作
- `PlatformMetadataPlugin.swift:14-17` - iOS 原生實作

## 測試配置

### 測試檔案
- `test/platform_metadata_test.dart` - 主要功能測試
- `test/platform_metadata_method_channel_test.dart` - Method Channel 測試
- `example/test/widget_test.dart` - 範例應用程式測試

### 執行測試
```bash
# 在根目錄執行插件測試
fvm flutter test

# 在 example 目錄執行應用程式測試
cd example && fvm flutter test
```

## 開發注意事項

1. **原生程式碼修改**: 修改 Android 或 iOS 原生程式碼後，需要重新編譯範例應用程式
2. **版本管理**: 使用 `fvm` 管理 Flutter 版本，確保一致性
3. **測試覆蓋**: 新增功能時需要同時更新對應的測試檔案
4. **文件同步**: 修改 API 後需要更新 README.md 和範例程式碼

## 範例使用

```dart
// 添加到 AndroidManifest.xml
<meta-data android:name="meta_key" android:value="meta_value from android" />

// 添加到 iOS info.plist
<key>meta_key</key>
<string>meta_value from ios</string>

// Dart 程式碼使用
String metaDataValue = await PlatformMetadata.getMetaDataValue('meta_key') ?? '';
```

## 問題排除

1. **Method Channel 未註冊**: 確保原生插件正確註冊
2. **元數據讀取失敗**: 檢查 AndroidManifest.xml 或 info.plist 中的鍵值是否正確
3. **建置失敗**: 確保 Flutter 版本與 pubspec.yaml 中的要求一致