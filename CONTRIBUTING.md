# Zasady pracy

## Gałęzie

`gałąź robocza → dev → main`

- Gałęzie robocze z prefiksem obszaru: `fw/`, `app/`, `ios/`, `android/`, `docs/`, `tools/`, `ci/`.
  PR zawsze do `dev`.
  - `app/` — wspólny kod Fluttera (ekrany, BLE, logika) — większość pracy nad aplikacją,
  - `ios/`, `android/` — tylko zmiany specyficzne dla platformy (`app/ios/`, `app/android/`:
    uprawnienia, podpisywanie, konfiguracja natywna).
- `dev` — integracja. `main` — działające demo, PR `dev` → `main` po teście na sprzęcie i obu telefonach.

## Merge

- do `dev`: **squash**; potem gałąź usuwamy i nie pracujemy na niej dalej,
- do `main`: **merge commit**, nigdy squash.

## PR

- Tytuł: `<obszar>: <opis>`, obszar: `fw`, `app`, `ios`, `android`, `docs`, `tools`, `ci`, `chore`, `release`.
  Np. `fw: add SCD41 readout`. Sprawdza to CI.
- Zmiana kontraktu BLE = firmware + `docs/ble-protocol.md` w jednym PR, akceptuje druga strona.
- Merge tylko z zielonym CI (`ci-ok`).

## Inne

- Końce linii ustawia `.gitattributes` — nie zmieniaj `core.autocrlf`.
- VS Code: otwieraj `social-paradox.code-workspace` — firmware i app jako osobne foldery
  (PlatformIO widzi projekt tylko, gdy `platformio.ini` leży w głównym katalogu folderu).

## Release

Tag na commicie z `main` (`m1`, `m3`, `demo`…) → GitHub Release z obrazem firmware'u
(`.bin`, wgrywany od adresu `0x0`) i APK na Androida.
