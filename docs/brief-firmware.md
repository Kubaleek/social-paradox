# Projekt: domowy miernik CO2 „kiedy i jak wietrzyć” — firmware (embedded)

## Kontekst
Budujemy na hackathon GrowUp urządzenie, które mierzy CO2, temperaturę i wilgotność
w małych pokojach (akademiki, wynajmowane mieszkania) i mówi, kiedy i jak długo wietrzyć.
Zespół: ja robię firmware, druga osoba aplikację we Flutterze (komunikacja przez BLE),
trzecia resztę. Pracujemy na macOS, Windowsie i Linuksie.

Twoje zadanie: firmware urządzenia. Nie pisz aplikacji mobilnej.

## Sprzęt
- M5StickC Plus2 (ESP32-PICO-V3-02, ekran ST7789 135x240, bateria, przyciski,
  wbudowana czerwona dioda, brzęczyk, mikrofon, IMU, RTC BM8563, port Grove).
- M5Stack CO2L Unit (Sensirion SCD41), podłączony kablem Grove do portu Grove M5Sticka.
  I2C, adres 0x62. Zakres 400–5000 ppm, dokładność ±(40 ppm + 5% odczytu).
- Zasilanie: USB-C do M5Sticka, który przez Grove zasila czujnik. Urządzenie zwykle
  stale podłączone do zasilania.
- Uwaga: ekran mojego egzemplarza ma poziomą linię uszkodzonych pikseli na środku.
  Układ ekranu ma być konfigurowalny (stała z przesunięciem głównej liczby).

Piny do ZWERYFIKOWANIA w oficjalnej dokumentacji M5StickC Plus2 przed użyciem
(nie zakładaj, sprawdź): Grove SDA=G32, SCL=G33; dioda G19; brzęczyk G2;
pin podtrzymania zasilania G4 (musi być HIGH przy pracy na baterii); przyciski A/B.

## Stack i zasady
- PlatformIO + framework Arduino. Wersje platformy i bibliotek przypięte w platformio.ini.
- Biblioteki: M5Unified (obsługa M5StickC Plus2, ekran, przyciski, zasilanie),
  oficjalna biblioteka Sensirion I2C SCD4x, NimBLE-Arduino do BLE.
  Przed użyciem sprawdź aktualne API bibliotek w ich przykładach/dokumentacji.
  Nie wymyślaj funkcji, których nie ma.
- Pętla nieblokująca (millis), bez delay() w głównej pętli.
- Kod modułowy: sensor/, ble/, display/, alerts/, storage/, analytics/, config/.
- Logika obliczeniowa (klasyfikacja, nachylenie, prognoza, wykrywanie wietrzenia)
  jako czyste funkcje C++ bez zależności od Arduino, z testami jednostkowymi
  w środowisku PlatformIO `native` (`pio test -e native`).
- Kod i komentarze po angielsku, dokumentacja w docs/ po polsku.
- Sekrety (jeśli kiedyś Wi-Fi) w pliku poza repo; do repo tylko przykład.
- Dodaj .gitattributes (`* text=auto`), bo zespół pracuje na trzech systemach.

## Wymagania dotyczące SCD41
- Tryb pomiaru okresowego (co 5 s) na start; architektura gotowa na tryb
  pojedynczego pomiaru SCD41 (oszczędzanie baterii) w przyszłości.
- Sprawdzaj gotowość danych przed odczytem. Pierwsze minuty po starcie oznaczaj
  jako rozgrzewanie (flaga w danych).
- Ustaw ciśnienie otoczenia lub wysokość (domyślnie Kraków, ok. 220 m n.p.m.,
  konfigurowalne).
- Offset temperatury konfigurowalny (samonagrzewanie w obudowie); kalibrujemy później.
- Automatyczna samokalibracja (ASC) domyślnie włączona; dodaj komendę wymuszonej
  rekalibracji (FRC) do 420 ppm na świeżym powietrzu, wywoływaną przyciskiem
  albo przez BLE, z potwierdzeniem.
- NIE zapisuj ustawień do pamięci trwałej czujnika w pętli (ograniczona liczba zapisów).

## Funkcje (kolejność = kolejność milestone'ów)

### M0 — szkielet
Projekt PlatformIO, M5Unified, ekran pokazuje „hello”, log przez Serial 115200.
Akceptacja: kompiluje się i wgrywa na M5StickC Plus2.

### M1 — czujnik
Skan I2C znajduje 0x62; odczyt CO2/temp/RH co 5 s; wypis na Serial jako CSV:
`ms_since_boot,co2_ppm,temp_c,rh_pct,flags`. Obsługa błędów I2C i ponownej inicjalizacji.
Akceptacja: przy chuchnięciu w czujnik CO2 wyraźnie rośnie.

### M2 — ekran i sygnalizacja
- Duża liczba ppm + kolor stanu: zielony < 800, żółty 800–1200, czerwony > 1200
  (progi konfigurowalne, histereza ok. 50 ppm, żeby nie migało na granicy).
- Ekran przyciemnia się/gaśnie po 30 s bezczynności, budzi przyciskiem A
  (mniej ciepła → dokładniejsza temperatura).
- Czerwona dioda/brzęczyk przy przejściu do stanu czerwonego, z limitem
  (nie częściej niż co X minut) i godzinami ciszy.
- Przycisk B: oznaczenie „właśnie otwieram okno” (zdarzenie w danych).

### M3 — BLE (kontrakt dla aplikacji Flutter)
- NimBLE, nazwa urządzenia np. „OddechPokoju-XXXX” (końcówka z MAC).
- Własny serwis GATT z charakterystykami:
  - Measurement (read + notify, co pomiar): binarnie little-endian:
    uint32 timestamp_s (unix, jeśli czas ustawiony, inaczej sekundy od startu),
    uint16 co2_ppm, int16 temp_c_x100, uint16 rh_pct_x100, uint8 state
    (0 zielony,1 żółty,2 czerwony), uint8 flags (bit0 rozgrzewanie,
    bit1 czas nieustawiony, bit2 trwa wietrzenie, bit3 błąd czujnika).
  - Time sync (write): uint32 unix time z telefonu → ustawia RTC.
  - Config (read/write): progi, offset temperatury, wysokość, godziny ciszy.
  - Command (write): FRC, oznaczenie wietrzenia, reset historii.
  - History (dla synchronizacji po rozłączeniu) — w M5.
- Wygeneruj UUID-y raz i zapisz je w docs/ble-protocol.md razem z dokładnym
  formatem bajtów, przykładami i opisem błędów. Ten plik to kontrakt dla
  osoby od Fluttera — ma wystarczyć, żeby napisała klienta bez czytania firmware'u.
- Akceptacja: aplikacja nRF Connect widzi urządzenie i odbiera notyfikacje.

### M4 — tryb symulacji
Flaga kompilacji/konfiguracji, w której zamiast czujnika generowany jest
realistyczny przebieg CO2 (powolny wzrost przy obecności ludzi, wykładniczy
spadek po „otwarciu okna”, szum). Ten sam BLE i CSV. Cel: osoby bez sprzętu
mogą rozwijać aplikację. Dodaj też skrypt/tryb odtwarzania nagranego CSV.

### M5 — historia
Bufor kołowy pomiarów (np. 1 próbka/min, min. 24 h) w RAM + okresowy zapis
do LittleFS, żeby przetrwał restart. Synchronizacja przez BLE od zadanego
timestampu. Udokumentuj protokół w docs/ble-protocol.md.

### M6 — analityka na urządzeniu (czyste funkcje + testy)
- Nachylenie CO2 (regresja liniowa z ostatnich 5–10 min) i prognoza
  „za ile minut przekroczysz próg czerwony”.
- Wykrywanie wietrzenia (gwałtowny spadek CO2, opcjonalnie spadek temperatury)
  i jego zakończenia.
- Udział powietrza wydychanego: f = (C_wewn − C_zewn) / 38000,
  C_zewn domyślnie 420 ppm (konfigurowalne). Wyślij jako dodatkowe pole
  albo zostaw aplikacji — zaproponuj i uzasadnij.
- Cięższe dopasowanie krzywej zaniku (uczenie się wymiany powietrza w pokoju)
  zostaje w aplikacji; firmware ma dostarczyć zdarzenia początku/końca wietrzenia
  i surowe dane.

## Sposób pracy
- Realizuj milestone po milestonie. Po każdym: krótko podsumuj, co zrobione,
  jak przetestować na sprzęcie, co niezweryfikowane.
- Gdy decyzja wpływa na aplikację (format BLE, jednostki, nazwy pól),
  zaproponuj ją i poczekaj na moją akceptację, zanim zamrozisz kontrakt.
- Jeśli czegoś nie da się sprawdzić bez sprzętu, napisz to wprost zamiast zgadywać.
- Utrzymuj README z instrukcją: instalacja PlatformIO, wgranie, monitor szeregowy,
  tryb symulacji, testy.

Zacznij od M0 i M1: zaproponuj strukturę repo i platformio.ini, potem kod.
