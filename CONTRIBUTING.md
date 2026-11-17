# Zasady pracy

## Gałęzie

`gałąź robocza → dev → main`

- Gałęzie robocze z prefiksem obszaru: `fw/`, `app/`, `docs/`, `tools/`, `ci/`. PR zawsze do `dev`.
- `dev` — integracja. `main` — działające demo, PR `dev` → `main` po teście na sprzęcie i obu telefonach.
- Brak gałęzi per platforma (iOS/Android) — Flutter to jeden kod.

## Merge

- do `dev`: **squash**; potem gałąź usuwamy i nie pracujemy na niej dalej,
- do `main`: **merge commit**, nigdy squash.

## PR

- Tytuł: `<obszar>: <opis>`, obszar: `fw`, `app`, `docs`, `tools`, `ci`, `chore`, `release`.
  Np. `fw: add SCD41 readout`. Sprawdza to CI.
- Zmiana kontraktu BLE = firmware + `docs/ble-protocol.md` w jednym PR, akceptuje druga strona.
- Merge tylko z zielonym CI (`ci-ok`).

## Inne

- Sekrety nigdy w repo, tylko pliki `*.example`.
- Końce linii ustawia `.gitattributes` — nie zmieniaj `core.autocrlf`.
