# SENTINEL - APK (Android)

Aplikacja to ta sama strona co na komputerze, opakowana w aplikację Android (Capacitor).
Telefon łączy się z serwerem SENTINEL uruchomionym na komputerze w tej samej sieci Wi-Fi.

## Zawartość repozytorium
- `www/` - strona aplikacji i biblioteki offline (`lib/`)
- `native/java/pl/sentinel/app/` - natywny skaner kodów (CameraX + ML Kit) i most do strony
- `scripts/patch-android.js` - przygotowanie projektu Android (uprawnienia, zależności, podpis, wersja)
- `.github/workflows/build-apk.yml` - automatyczne budowanie APK na GitHub
- `capacitor.config.json`, `package.json` - konfiguracja

Folder `android/` nie jest w repozytorium. Powstaje sam podczas budowania.

## Aktualizacja bez usuwania starej aplikacji
Android nadpisze aplikację, jeśli nowy APK ma tę samą nazwę pakietu, ten sam podpis i wyższy numer wersji.
Build w Actions spełnia to automatycznie, o ile raz skonfigurujesz klucz podpisu.

### Krok jednorazowy: klucz podpisu
1. Na komputerze z Javą (np. z Android Studio) wykonaj w wierszu poleceń:
   ```
   keytool -genkeypair -v -keystore sentinel.keystore -alias sentinel -keyalg RSA -keysize 2048 -validity 10000
   ```
   Zapisz hasło do magazynu kluczy i hasło klucza.
2. Zakoduj plik w Base64 (PowerShell):
   ```
   [Convert]::ToBase64String([IO.File]::ReadAllBytes("sentinel.keystore")) | Set-Content keystore.txt
   ```
3. W repozytorium na GitHub: **Settings -> Secrets and variables -> Actions -> New repository secret** i dodaj:
   - `KEYSTORE_BASE64` - zawartość pliku `keystore.txt`
   - `KEYSTORE_PASSWORD` - hasło magazynu kluczy
   - `KEY_ALIAS` - `sentinel`
   - `KEY_PASSWORD` - hasło klucza
4. **Zachowaj plik `sentinel.keystore`** w bezpiecznym miejscu i nie wgrywaj go na GitHub. Bez niego nie da się wydać aktualizacji nad tą wersją.

### Pierwsza instalacja z nowym kluczem
Poprzednie APK były podpisane innym kluczem, więc raz trzeba odinstalować starą aplikację i zainstalować nową.
Od tego momentu każdy kolejny APK instaluje się nad poprzednim bez usuwania.

## Budowanie APK
1. Wgraj zawartość repozytorium (zastąp pliki).
2. Wejdź w **Actions -> Build APK -> Run workflow** (lub zrób zwykły commit).
3. Po zakończeniu pobierz artefakt **SENTINEL-apk** i zainstaluj `app-debug.apk` na telefonie (przy instalacji nad starą wersją zezwól na aktualizację).

## Ważne
- Komputer z serwerem musi być włączony, a telefon w tej samej sieci Wi-Fi.
- Zapora Windows musi zezwalać na połączenia do portu 3000.
- Zmiana adresu serwera: przycisk statusu w aplikacji (lewy dolny róg) -> Zapisz adres.
- Aktualizacja strony: podmień `www/index.html`, wgraj na GitHub i zbuduj ponownie.
