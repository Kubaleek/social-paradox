# firmware

Firmware urządzenia (M5StickC Plus2 + SCD41), PlatformIO + Arduino.

Jeszcze pusty — wymagania: [`../docs/brief-firmware.md`](../docs/brief-firmware.md).
Instrukcja budowania, wgrywania, symulacji i testów pojawi się tu w M0/M1.

## Wymagania CI

CI (`.github/workflows/ci.yml`) startuje, gdy istnieje `firmware/platformio.ini`:

- `pio run` buduje środowiska z `default_envs` — środowisko `native` **nie** może tam być;
- `pio test -e native` uruchamia testy jednostkowe, jeśli istnieje `[env:native]`;
- `clang-format --dry-run --Werror` na wszystkich plikach C/C++ (styl w `.clang-format`).

Przed pushem lokalnie, w wersjach z `ci.yml`:

```sh
pip install "platformio==6.2.0" "clang-format==23.1.5"
git ls-files -z -- '*.c' '*.cpp' '*.h' '*.hpp' '*.ino' | xargs -0 clang-format -i
pio run
pio test -e native
```
