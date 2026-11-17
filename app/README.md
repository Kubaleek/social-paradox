# app

Aplikacja mobilna (Flutter) — jeden kod dla iOS i Androida.

Jeszcze pusta. Projekt generuje osoba od aplikacji, z tego katalogu:

```sh
flutter create --org <odwrocona.domena> --project-name oddech_pokoju --platforms ios,android .
```

`flutter create` doda własny `.gitignore` — zostaw go.

Komunikacja z urządzeniem: wyłącznie przez kontrakt BLE w
[`../docs/ble-protocol.md`](../docs/ble-protocol.md) (powstanie w M3).

Do zapamiętania przy BLE:
- iOS: build tylko na macOS z Xcode; w `ios/Runner/Info.plist` wymagany
  `NSBluetoothAlwaysUsageDescription`.
- Android 12+: uprawnienia `BLUETOOTH_SCAN` i `BLUETOOTH_CONNECT`
  (starsze wersje: lokalizacja do skanowania).
