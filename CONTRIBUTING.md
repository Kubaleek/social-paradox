# Zasady pracy

## Gałęzie

`gałąź robocza → dev → main`

- Gałęzie robocze z prefiksem obszaru: `fw/`, `app/`, `ios/`, `android/`, `docs/`, `tools/`, `ci/`.
  PR zawsze do `dev`.
  - `app/` — wspólny kod Fluttera (ekrany, BLE, logika) — większość pracy nad aplikacją,
  - `ios/`, `android/` — tylko zmiany specyficzne dla platformy (`app/ios/`, `app/android/`:
    uprawnienia, podpisywanie, konfiguracja natywna).
- `dev` — integracja. `main` — działające demo, PR `dev` → `main` po teście na sprzęcie i obu telefonach.
- Gałęzie `ios/` i `android/` są krótkie jak każda robocza — nie ma długo żyjących gałęzi per platforma.

## Merge

- do `dev`: **squash**; potem gałąź usuwamy i nie pracujemy na niej dalej,
- do `main`: **merge commit**, nigdy squash.

## PR

- Tytuł: `<obszar>: <opis>`, obszar: `fw`, `app`, `ios`, `android`, `docs`, `tools`, `ci`, `chore`, `release`.
  Np. `fw: add SCD41 readout`. Sprawdza to CI.
- Zmiana kontraktu BLE = firmware + `docs/ble-protocol.md` w jednym PR, akceptuje druga strona.
- Merge tylko z zielonym CI (`ci-ok`).

## Inne

- Sekrety nigdy w repo, tylko pliki `*.example`.
- Końce linii ustawia `.gitattributes` — nie zmieniaj `core.autocrlf`.
