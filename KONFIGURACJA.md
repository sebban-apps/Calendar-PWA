# Kalendarz nawyków · dokumentacja techniczna

## Przegląd

Statyczna aplikacja PWA w jednym pliku HTML (HTML, CSS i JavaScript bez frameworków i bez kroku budowania). Dane trzymane są lokalnie w przeglądarce. Hosting przez GitHub Pages, publikacja przez GitHub Actions.

Wygląd zgodny z systemem **Modern Teal Signature** (tokeny kolorów, Manrope i Inter, promienie 10/12/16/20 px, jasny i ciemny motyw).

## Technologie i zależności

| Element | Szczegóły |
|---|---|
| Kod | czysty HTML, CSS, JavaScript (ES2017+, `async/await`) |
| Czcionki | Google Fonts, Manrope (500, 600, 700) i Inter (400, 500, 600) |
| Magazyn danych | `localStorage`, w Claude `window.storage`, awaryjnie pamięć RAM |
| Opcjonalne API | File System Access API (`showSaveFilePicker`), Web Share API |
| Hosting | GitHub Pages |

Brak zależności npm, brak kluczy API, brak zmiennych środowiskowych.

## Struktura plików

```
.
├── .github/
│   └── workflows/
│       └── deploy.yml        # publikacja na GitHub Pages
├── index.html                # przekierowanie na kalendarz-nawykow.html
├── kalendarz-nawykow.html    # cała aplikacja
├── manifest.json             # manifest PWA
├── icon-192.png              # ikona aplikacji 192×192
├── icon-512.png              # ikona aplikacji 512×512
├── README.md                 # instrukcja dla użytkownika
└── KONFIGURACJA.md           # ten plik
```

## Uruchomienie lokalne

Wymagany dowolny serwer statyczny (manifest i część API nie działają poprawnie z `file://`).

```bash
python3 -m http.server 8000
```

Następnie otwórz `http://localhost:8000/kalendarz-nawykow.html`.

## Architektura kodu

Skrypt w `kalendarz-nawykow.html` jest jednym IIFE z trybem `strict`. Najważniejsze części

- **`Store`** wybiera magazyn. Kolejność to `window.storage` (środowisko Claude), potem `localStorage`, na końcu `Map` w pamięci.
- **`loadJSON` i `saveJSON`** serializują dane. Każdy zapis uruchamia `scheduleLiveMirror()` (zapis do podpiętego pliku z opóźnieniem 800 ms).
- **Serie powtarzalne.** `addEntry` tworzy osobną serię (`seriesId`) dla każdego wybranego dnia tygodnia. `regenerateSeries` przy starcie dopisuje wystąpienia do horyzontu `RECURRING_HORIZON_WEEKS` (12 tygodni). `endSeriesFrom` kończy serię od danej daty, historia zostaje.
- **Render.** `renderAll` wywołuje `renderVisRow`, `renderCalendar` i `renderResultsOrDay`. Widok wyszukiwania zastępuje karty dnia i dodawania.
- **Motyw.** Krótki skrypt w `<head>` ustawia `data-theme` na `<html>` przed narysowaniem strony. Przycisk `#themeToggle` przełącza motyw i zapisuje wybór.
- **`showToast(msg, kind)`** pokazuje krótkie potwierdzenie akcji (dodanie, edycja, usunięcie, zmiany kategorii).

### Klucze w magazynie

Wszystkie klucze w `localStorage` mają prefiks `kalendarz-nawykow:`.

| Klucz | Zawartość |
|---|---|
| `entries` | tablica wpisów `{id, date, time, category, subtitle, note, favorite, seriesId}` |
| `categories` | tablica nazw kategorii |
| `hiddenCategories` | kategorie ukryte w widoku |
| `seriesMeta` | `{[seriesId]: {category, weekday, time, generatedUntil, endedAt}}` |
| `categoryColors` | własne kolory kategorii `{nazwa: "#RRGGBB"}` |
| `lastAutoBackup` | data ostatniej kopii `RRRR-MM-DD` |
| `theme` | `light` albo `dark` (zapisywany zawsze bezpośrednio w `localStorage`) |

Format pliku kopii ma pole `app: "kalendarz-nawykow"` i `version: 1`. Import przechodzi przez `sanitizeBackupPayload`, które odrzuca niepoprawne rekordy.

## Ważne parametry

| Stała | Wartość | Znaczenie |
|---|---|---|
| `RECURRING_HORIZON_WEEKS` | `12` | na ile tygodni do przodu generowane są serie |
| `AUTO_BACKUP_INTERVAL_DAYS` | `7` | co ile dni automatyczna kopia |
| `BACKUP_VERSION` | `1` | wersja formatu kopii |
| `DEFAULT_CATEGORIES` | 4 nazwy | kategorie startowe |
| `CATEGORY_COLOR_PALETTE` | 12 kolorów | paleta kolorów kategorii |

### Tokeny wyglądu

Kolory, promienie i odstępy są zdefiniowane jako zmienne CSS w `:root` (motyw jasny) i `:root[data-theme="dark"]` (motyw ciemny). Najważniejsze to `--bg`, `--surface`, `--surface-2`, `--text`, `--text-2`, `--border`, `--accent`, `--accent-mist`, `--success`, `--error`, `--r-sm`, `--r-btn`, `--r-card`. Nowe elementy powinny używać tych tokenów zamiast wpisanych na sztywno kolorów. Wyjątkiem są kolory kategorii, bo to dane użytkownika.

Kolor paska systemowego ustawia `<meta name="theme-color">` (aktualizowany przy zmianie motywu) oraz `theme_color` w `manifest.json`.

## Publikacja na GitHub Pages

Wymagane jednorazowo

1. Plik workflow musi leżeć w `.github/workflows/deploy.yml`.
2. W repozytorium **Settings → Pages → Build and deployment → Source** ustaw **GitHub Actions**.

Publikacja uruchamia się przy każdym `push` na gałąź `main` albo ręcznie z zakładki **Actions** (`workflow_dispatch`). Workflow kopiuje do `_site` pliki `index.html`, `kalendarz-nawykow.html`, `manifest.json`, `icon-192.png` i `icon-512.png`.

**Dodając nowy plik potrzebny aplikacji, dopisz go do polecenia `cp` w `deploy.yml`**, inaczej nie trafi na stronę. Pliki `.md` nie są publikowane.

## Testowanie

Brak automatycznych testów w repozytorium. Elementy interfejsu mają atrybuty `data-testid` (np. `add-save`, `cal-grid`, `theme-toggle`), co ułatwia testy w Playwright albo podobnym narzędziu.

Ręczna lista kontrolna po zmianach

- dodanie wpisu zwykłego i powtarzalnego, błędna godzina (np. `25:00`) pokazuje komunikat,
- edycja, gwiazdka, usuwanie jednego dnia i całej serii,
- wyszukiwanie i filtr ulubionych,
- dodanie, zmiana nazwy, koloru i usunięcie kategorii,
- eksport i import kopii,
- przełączenie motywu i ponowne wczytanie strony,
- widok na szerokości telefonu (ok. 390 px) i komputera (ponad 960 px),
- brak błędów w konsoli przeglądarki.

## Rozwiązywanie problemów

**Strona po publikacji się nie zmienia.** Sprawdź status workflow w zakładce Actions. Zainstalowana aplikacja może trzymać starą wersję w pamięci podręcznej, pomaga odświeżenie albo ponowne otwarcie.

**Nowa ikona nie pojawia się na telefonie.** System zapamiętuje ikonę przy instalacji. Usuń aplikację z ekranu głównego i dodaj ją ponownie (dane w przeglądarce zostają, ale warto najpierw zrobić eksport kopii).

**Dane zniknęły.** Najczęściej przez wyczyszczenie danych witryny albo tryb prywatny. Przywróć dane z ostatniego pliku kopii.

**Brak opcji „połącz z plikiem na dysku”.** Przeglądarka nie obsługuje File System Access API (np. Firefox, Safari, telefony). Opcja jest wtedy ukryta.

**Czcionki wyglądają inaczej.** Google Fonts się nie wczytało (brak internetu albo blokada). Aplikacja korzysta wtedy z czcionki systemowej.

## Wersjonowanie i aktualizacje

- Zmiany wprowadzaj w `kalendarz-nawykow.html` i wypychaj na `main`, publikacja odbywa się automatycznie.
- Zmiana struktury danych wymaga podniesienia `BACKUP_VERSION` i obsługi starszych plików w `sanitizeBackupPayload`.
- Po każdej zmianie funkcji zaktualizuj `README.md` i ten plik.
