# Oddech Pokoju

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

## Kontrakt firmware ↔ aplikacja

Jedynym punktem styku jest [`docs/ble-protocol.md`](docs/ble-protocol.md)
(powstanie w M3). Zmiana formatu BLE = zmiana tego pliku w tym samym
commicie/PR co firmware, z akceptacją osoby od aplikacji.

## Zasady

- Gałęzie: `feature → dev → main`.
  - Robocze, krótkie, z prefiksem obszaru: `fw/...`, `app/...`, `docs/...`, `tools/...`
    (zmiany tylko pod jedną platformę: np. `app/ios-permissions`). PR do `dev`.
  - `dev` — integracja; tu testujemy firmware razem z aplikacją.
  - `main` — zawsze działające demo; merge z `dev` dopiero po teście na sprzęcie
    i na obu telefonach (iOS + Android). Stabilne punkty tagujemy (`m1`, `m3`, `demo`).
  - Nie ma gałęzi per platforma — Flutter to jeden kod dla iOS i Androida.
  - Zmiana kontraktu BLE: firmware + `docs/ble-protocol.md` w jednym PR,
    akceptuje osoba od aplikacji.
- Końce linii normalizuje `.gitattributes` — nie zmieniaj `core.autocrlf` na siłę.
- Sekrety nigdy w repo; commitujemy tylko pliki `*.example`.
