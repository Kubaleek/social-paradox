# Zasady pracy

## Gałęzie

```
fw/... app/... docs/... tools/... ci/...  ──squash──▶  dev  ──merge commit──▶  main
```

- **Gałęzie robocze** — krótkie, z prefiksem obszaru: `fw/`, `app/`, `docs/`, `tools/`, `ci/`.
  Zmiana tylko pod jedną platformę to zwykła gałąź, np. `app/ios-permissions`.
  Nie ma gałęzi per platforma — Flutter to jeden kod dla iOS i Androida.
- **`dev`** — integracja; tu testujemy firmware razem z aplikacją. Każdy PR roboczy idzie do `dev`.
- **`main`** — zawsze działające demo. PR `dev` → `main` dopiero po teście na sprzęcie
  i na obu telefonach (iOS + Android). Stabilne punkty tagujemy na `main`: `m1`, `m3`, `demo`…

## Merge

| Kierunek | Metoda | Dlaczego |
|---|---|---|
| gałąź robocza → `dev` | **squash** | jeden commit na funkcję, łatwy `git revert` |
| `dev` → `main` | **merge commit** (`--no-ff`) | historia `dev` zostaje przodkiem `main`; squash dawałby konflikty przy każdym kolejnym merge'u |

Po squashu gałąź roboczą usuwamy (GitHub robi to automatycznie) i **nie pracujemy na niej dalej** —
kolejna zmiana = nowa gałąź od aktualnego `dev`.

## Tytuły PR

Przy squashu tytuł PR staje się commitem w `dev`, więc ma format:

```
<obszar>: <co, w trybie rozkazującym, po angielsku>
```

Obszary: `fw`, `app`, `docs`, `tools`, `ci`, `chore`, `release` (PR `dev` → `main`).
Przykłady: `fw: add SCD41 periodic measurement`, `app: show live CO2 reading`,
`release: m1`. Format sprawdza CI (job `pr-title`).

## Kontrakt BLE

Zmiana formatu BLE = firmware + `docs/ble-protocol.md` w **jednym PR**.
CODEOWNERS wymusza recenzję osoby od aplikacji (i odwrotnie dla zmian w samym dokumencie).

## CI

Jeden workflow [`.github/workflows/ci.yml`](.github/workflows/ci.yml) na każdy PR do `dev`/`main`
i push do tych gałęzi. Uruchamia tylko joby dla zmienionych katalogów:

| Job | Kiedy | Co robi |
|---|---|---|
| `pr-title` | PR | format tytułu |
| `firmware` | zmiany w `firmware/` | `clang-format`, `pio run` (domyślne środowiska), `pio test -e native` |
| `app` | zmiany w `app/` | `dart format`, `flutter analyze`, `flutter test`, build APK (debug) |
| `ci-ok` | zawsze | zbiorczy wynik — **jedyny wymagany check** w rulesetach |

Joby `firmware`/`app` startują dopiero, gdy w katalogu jest projekt
(`firmware/platformio.ini`, `app/pubspec.yaml`). iOS nie jest budowany w CI
(runnery macOS) — sprawdzamy go lokalnie na Macu przed PR `dev` → `main`.

Przed pushem lokalnie: patrz `firmware/README.md` i `app/README.md`.

## Wersje narzędzi

- Firmware: wersje platformy i bibliotek przypięte w `firmware/platformio.ini`;
  wersja PlatformIO Core i `clang-format` — w `ci.yml` (`env`), lokalnie używaj tych samych.
- Aplikacja: wersja Fluttera przypięta w `app/pubspec.yaml` (`environment: flutter:`),
  `pubspec.lock` commitujemy.

## Pozostałe

- Końce linii normalizuje `.gitattributes` — nie ustawiaj `core.autocrlf` na siłę.
- Sekrety nigdy w repo; commitujemy tylko pliki `*.example`.

## Konfiguracja GitHuba (raz, przez admina repo)

> Rulesety na **prywatnym** repo wymagają planu Pro/Team. Na darmowym planie działają
> tylko w repo **publicznym**.

1. **Settings → General → Pull Requests:**
   - zaznacz *Allow merge commits* i *Allow squash merging*, odznacz *Allow rebase merging*;
   - squash: *Default commit message* → *Pull request title*;
   - zaznacz *Automatically delete head branches*.
2. **Settings → Branches → Default branch:** `dev` (PR-y domyślnie trafiają do `dev`).
3. **Settings → Rules → Rulesets → New ruleset → Import a ruleset:** zaimportuj
   [`.github/rulesets/dev.json`](.github/rulesets/dev.json) i
   [`.github/rulesets/main.json`](.github/rulesets/main.json).
   Oba: tylko przez PR, bez force-push i usuwania gałęzi, wymagany check `ci-ok`,
   wymagana recenzja CODEOWNERS; `dev` dopuszcza tylko squash, `main` tylko merge commit.
