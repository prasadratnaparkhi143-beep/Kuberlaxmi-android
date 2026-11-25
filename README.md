# Kuberlaxmi-name: Build Android APK

on:
  workflow_dispatch:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: '17'

      - uses: gradle/actions/setup-gradle@v4
        with:
          gradle-version: '8.9'

      - name: Extract project ZIP
        run: |
          mkdir extracted
          ZIP=$(find . -name '*.zip' -type f | head -n 1)
          unzip -q "$ZIP" -d extracted

      - name: Build APK
        run: |
          ROOT=$(find extracted -name settings.gradle -type f | head -n 1 | xargs dirname)
          gradle -p "$ROOT" assembleDebug

      - uses: actions/upload-artifact@v4
        with:
          name: Kuberlaxmi-APK
          path: extracted/**/app/build/outputs/apk/debug/app-debug.apk
