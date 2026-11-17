# Social Paradox

Domowy miernik CO2 „kiedy i jak wietrzyć” — projekt na hackathon GrowUp.
Urządzenie mierzy CO2, temperaturę i wilgotność w małych pokojach i mówi,
kiedy i jak długo wietrzyć. Aplikacja mobilna łączy się z nim przez BLE.

## Struktura repo

| Katalog     | Zawartość                                              | Właściciel       |
|-------------|--------------------------------------------------------|------------------|
| `firmware/` | Firmware M5StickC Plus2 + SCD41 (PlatformIO, Arduino)  | firmware         |
| `app/`      | Aplikacja Flutter (iOS + Android, jeden kod)           | aplikacja        |
| `docs/`     | Dokumentacja (po polsku), w tym kontrakt BLE           | wszyscy          |
| `tools/`    | Skrypty pomocnicze (odtwarzanie CSV itp.)              | wszyscy          |
| `.github/`  | CI, CODEOWNERS, szablon PR, rulesety gałęzi            | wszyscy          |

## Kontrakt firmware ↔ aplikacja

Jedynym punktem styku jest [`docs/ble-protocol.md`](docs/ble-protocol.md)
(powstanie w M3). Zmiana formatu BLE = zmiana tego pliku w tym samym
PR co firmware, z akceptacją osoby od aplikacji.

## Zasady pracy

Gałęzie, merge, tytuły PR, CI i konfiguracja GitHuba:
[`CONTRIBUTING.md`](CONTRIBUTING.md).
