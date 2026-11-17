# app

Aplikacja mobilna (Flutter) — jeden kod dla iOS i Androida.

Jeszcze pusta. Projekt generuje osoba od aplikacji, z tego katalogu:

```sh
flutter create --org <odwrocona.domena> --project-name social_paradox --platforms ios,android .
```

`flutter create` doda własny `.gitignore` — zostaw go. `pubspec.lock` commitujemy.

## Wymagania CI

CI (`.github/workflows/ci.yml`) startuje, gdy istnieje `app/pubspec.yaml`. Wersję Fluttera
czyta z `pubspec.yaml` — wpisz **dokładną** wersję (tę, której używa zespół):

```yaml
environment:
  sdk: ...            # zostaw to, co wygenerował flutter create
  flutter: 3.xx.y     # dokładna wersja z `flutter --version`
```

Przed pushem lokalnie (to samo sprawdza CI):

```sh
dart format .
flutter analyze
flutter test
```

iOS nie jest budowany w CI — przed PR `dev` → `main` zbuduj i uruchom na iPhonie z Maca.

## BLE

Komunikacja z urządzeniem: wyłącznie przez kontrakt BLE w
[`../docs/ble-protocol.md`](../docs/ble-protocol.md) (powstanie w M3).

- iOS: w `ios/Runner/Info.plist` wymagany `NSBluetoothAlwaysUsageDescription`.
- Android 12+: uprawnienia `BLUETOOTH_SCAN` i `BLUETOOTH_CONNECT`
  (starsze wersje: lokalizacja do skanowania).
