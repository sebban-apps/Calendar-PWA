# Kalendarz nawyków · dokumentacja techniczna

## Przegląd

Statyczna aplikacja PWA w jednym pliku HTML (HTML, CSS i JavaScript bez frameworków i bez kroku budowania). Dane trzymane są lokalnie w przeglądarce. Hosting przez GitHub Pages w organizacji `sebban-apps`, publikacja przez GitHub Actions.

| | |
|---|---|
| Repozytorium | https://github.com/sebban-apps/Calendar-PWA |
| Strona | https://sebban-apps.github.io/Calendar-PWA/ |
| Aplikacja | https://sebban-apps.github.io/Calendar-PWA/kalendarz-nawykow.html |

Wygląd zgodny z systemem **Modern Teal Signature** (tokeny kolorów, Manrope i Inter, promienie 10/12/16/20 px, jasny i ciemny motyw).

## Technologie i zależności

| Element | Szczegóły |
|---|---|
| Kod | czysty HTML, CSS, JavaScript (ES2017+, `async/await`) |
| Czcionki | Google Fonts, Manrope (500, 600, 700) i Inter (400, 500, 600) |
| Magazyn danych | `localStorage`, w Claude `window.storage`, awaryjnie pamięć RAM |
| Okno ustawień | element `<dialog>` z `showModal()` (Chrome, Edge, Firefox, Safari 15.4+) |
| Opcjonalne API | File System Access API (`showSaveFilePicker`), Web Share API, `HTMLInputElement.showPicker()` |

Brak zależności npm, brak kluczy API, brak zmiennych środowiskowych, brak service workera.

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

W repozytorium nie powinno być innych plików. Kopia `dziennik-nauki.html` i drugi `deploy.yml` w katalogu głównym należą do innego projektu albo są zbędne i trzeba je usunąć (patrz „Tożsamość PWA”).

## Uruchomienie lokalne

Wymagany dowolny serwer statyczny (manifest i część API nie działają z `file://`). Żeby ścieżki zgadzały się z GitHub Pages, serwuj katalog nadrzędny i otwieraj aplikację pod `/Calendar-PWA/`.

```bash
mkdir -p /tmp/strona && cp -r . /tmp/strona/Calendar-PWA
python3 -m http.server 8000 -d /tmp/strona
```

Następnie otwórz `http://localhost:8000/Calendar-PWA/kalendarz-nawykow.html`.

## Tożsamość PWA i manifest

Kilka aplikacji (Calendar-PWA, Journal-PWA, training-log) działa pod tym samym originem `https://sebban-apps.github.io`. Każda musi mieć osobną tożsamość, inaczej Chrome może uznać, że aplikacja „jest już zainstalowana”.

| Pole | Wartość | Uwagi |
|---|---|---|
| `id` | `/Calendar-PWA/kalendarz-nawykow.html` | stały identyfikator, **nie zmieniać** |
| `start_url` | `./kalendarz-nawykow.html` | wskazuje plik aplikacji |
| `scope` | `./` | czyli `/Calendar-PWA/`, nie obejmuje innych repozytoriów |
| `display` | `standalone` | |
| `theme_color`, `background_color` | `#101A20` | kolor ekranu startowego i paska |
| `icons` | `icon-192.png`, `icon-512.png` | PNG w podanych rozmiarach |

**Dlaczego taki `id`.** Wcześniej manifest nie miał pola `id`, więc Chrome wyliczał je z `start_url`, co daje `https://sebban-apps.github.io/Calendar-PWA/kalendarz-nawykow.html`. Ustawiony `id` rozwiązuje się dokładnie do tego samego adresu, więc już zainstalowane kopie aplikacji zachowują tożsamość. Pole `id` jest rozwiązywane względem originu, a nie katalogu manifestu, dlatego zaczyna się od `/Calendar-PWA/`, a nie od `./`.

**Konsekwencje zmian.**

- Zmiana `id`, nazwy repozytorium albo organizacji tworzy nową aplikację. Starą instalację trzeba usunąć ręcznie, a dane przenieść plikiem kopii (`localStorage` jest przypisany do originu).
- Zmiana `start_url` bez zmiany `id` jest bezpieczna dla tożsamości.
- Nie ustawiaj `scope` na `/`, bo objąłby inne aplikacje z `sebban-apps.github.io`.

**Czystość repozytorium.** Jeśli Pages publikuje z gałęzi zamiast z Actions, każdy plik z repo trafia na stronę. Obca strona w katalogu `/Calendar-PWA/` (np. kopia `dziennik-nauki.html`) podpięłaby manifest kalendarza. Dlatego w repo mają być tylko pliki z listy powyżej.

**Weryfikacja po publikacji.**

1. Otwórz `https://sebban-apps.github.io/Calendar-PWA/manifest.json` i sprawdź, czy zawiera pole `id`.
2. W Chrome otwórz aplikację, DevTools → Application → Manifest. Pole „Computed App ID” ma być `https://sebban-apps.github.io/Calendar-PWA/kalendarz-nawykow.html`, a lista błędów pusta.
3. Zainstaluj Kalendarz, potem Dziennik nauki (`/Journal-PWA/`). Obie instalacje mają się udać i otwierać właściwą aplikację.

## Architektura kodu

Skrypt w `kalendarz-nawykow.html` jest jednym IIFE z trybem `strict`. Najważniejsze części

- **`Store`** wybiera magazyn. Kolejność to `window.storage` (środowisko Claude), potem `localStorage`, na końcu `Map` w pamięci.
- **`loadJSON` i `saveJSON`** serializują dane. Każdy zapis uruchamia `scheduleLiveMirror()` (zapis do podpiętego pliku z opóźnieniem 800 ms).
- **Serie powtarzalne.** `addEntry` tworzy osobną serię (`seriesId`) dla każdego wybranego dnia tygodnia. `regenerateSeries` przy starcie dopisuje wystąpienia do horyzontu `RECURRING_HORIZON_WEEKS`. `endSeriesFrom` kończy serię od danej daty, historia zostaje.
- **Render.** `renderAll` wywołuje `renderVisRow`, `renderCalendar` i `renderResultsOrDay`. Gdy szukanie jest aktywne, `<body>` dostaje klasę `searching`, karta wyników zastępuje karty dnia i dodawania, a na ekranach węższych niż 960 px chowany jest też kalendarz.
- **Wyniki.** `renderResults` dzieli wpisy na „Do dziś” (malejąco) i „Zaplanowane” (rosnąco). Data przy wyniku to przycisk `data-act="goto"`, który czyści szukanie i wywołuje `jumpToDate`.
- **Okno ustawień.** `openSettings(sekcja)` resetuje stan menedżera kategorii, renderuje go i otwiera `<dialog id="settingsDlg">`. Toast jest na czas otwarcia przenoszony do okna, bo okno modalne leży w warstwie nad resztą strony.
- **Import.** `importFile` → walidacja (`JSON.parse`, odrzucenie pola `app` innego niż `kalendarz-nawykow`, `sanitizeBackupPayload`) → podgląd w `#importConfirm` → `applyPendingImport` zapisuje dane.
- **Motyw.** Krótki skrypt w `<head>` ustawia `data-theme` na `<html>` przed narysowaniem strony. Przycisk `#themeToggle` przełącza motyw i zapisuje wybór.
- **Pomocnicze.** `showToast(msg, kind)` pokazuje potwierdzenie akcji, `plWpis(n)` daje poprawną odmianę („wpis”, „wpisy”, „wpisów”).

### Klucze w magazynie

Wszystkie klucze w `localStorage` mają prefiks `kalendarz-nawykow:`, więc nie kolidują z innymi aplikacjami na tym samym originie (Dziennik nauki używa `dziennik-nauki:`).

| Klucz | Zawartość |
|---|---|
| `entries` | tablica wpisów `{id, date, time, category, subtitle, note, favorite, seriesId}` |
| `categories` | tablica nazw kategorii |
| `hiddenCategories` | kategorie ukryte w widoku |
| `seriesMeta` | `{[seriesId]: {category, weekday, time, generatedUntil, endedAt}}` |
| `categoryColors` | własne kolory kategorii `{nazwa: "#RRGGBB"}` |
| `lastAutoBackup` | data ostatniej kopii `RRRR-MM-DD` |
| `theme` | `light` albo `dark` (zawsze bezpośrednio w `localStorage`) |

### Format pliku kopii

```json
{
  "app": "kalendarz-nawykow",
  "version": 1,
  "exportedAt": "2026-11-04T08:04:00.000Z",
  "entries": [],
  "categories": [],
  "seriesMeta": {},
  "hiddenCategories": [],
  "categoryColors": {}
}
```

Format jest **niezmieniony** względem wcześniejszych wersji (`BACKUP_VERSION = 1`). Zgodność sprawdzono w obie strony, kopia ze starej wersji wczytana do nowej i kopia z nowej wczytana do starej dają identyczne dane we wszystkich pięciu kluczach. Pliki bez pola `app` są nadal akceptowane.

## Ważne parametry

| Stała | Wartość | Znaczenie |
|---|---|---|
| `RECURRING_HORIZON_WEEKS` | `12` | na ile tygodni do przodu generowane są serie |
| `AUTO_BACKUP_INTERVAL_DAYS` | `7` | co ile dni automatyczna kopia |
| `BACKUP_VERSION` | `1` | wersja formatu kopii |
| `DEFAULT_CATEGORIES` | 4 nazwy | kategorie startowe |
| `CATEGORY_COLOR_PALETTE` | 12 kolorów | paleta kolorów kategorii |

### Tokeny wyglądu

Kolory, promienie i odstępy są zmiennymi CSS w `:root` (motyw jasny) i `:root[data-theme="dark"]` (motyw ciemny). Najważniejsze to `--bg`, `--surface`, `--surface-2`, `--text`, `--text-2`, `--border`, `--accent`, `--accent-mist`, `--success`, `--error`, `--r-sm`, `--r-btn`, `--r-card`, `--r-lg`. Nowe elementy powinny używać tych tokenów. Wyjątkiem są kolory kategorii, bo to dane użytkownika.

Punkty przełamania układu to 600 px (większe odstępy i czcionki) i 960 px (dwie kolumny, przyklejony kalendarz).

## Publikacja na GitHub Pages

Wymagane jednorazowo

1. Plik workflow musi leżeć w `.github/workflows/deploy.yml`.
2. W repozytorium **Settings → Pages → Build and deployment → Source** ustaw **GitHub Actions**.

Publikacja uruchamia się przy każdym `push` na gałąź `main` albo ręcznie z zakładki **Actions** (`workflow_dispatch`). Workflow kopiuje do `_site` pliki `index.html`, `kalendarz-nawykow.html`, `manifest.json`, `icon-192.png` i `icon-512.png`.

**Dodając nowy plik potrzebny aplikacji, dopisz go do polecenia `cp` w `deploy.yml`**, inaczej nie trafi na stronę. Pliki `.md` nie są publikowane.

## Testowanie

Brak automatycznych testów w repozytorium. Elementy interfejsu mają atrybuty `data-testid` (np. `add-save`, `cal-grid`, `settings-btn`, `import-apply`), co ułatwia testy w Playwright.

Ręczna lista kontrolna po zmianach

- dodanie wpisu zwykłego i powtarzalnego, błędna godzina (np. `25:00`) pokazuje komunikat,
- nowa kategoria przez „+ nowa” w formularzu,
- edycja, gwiazdka, usuwanie jednego dnia i całej serii,
- wyszukiwanie, filtr ulubionych, przejście do dnia z wyników,
- okno ustawień, zmiana nazwy, koloru i usunięcie kategorii, zamykanie Escape i kliknięciem w tło,
- eksport kopii i import (podgląd, „Zastąp dane”, odrzucenie pliku innej aplikacji),
- import pliku kopii zrobionego starszą wersją,
- przełączenie motywu i ponowne wczytanie strony,
- widok na szerokości telefonu (360 i 390 px) i komputera (ponad 960 px),
- brak błędów w konsoli przeglądarki.

## Rozwiązywanie problemów

**„Ta aplikacja jest już zainstalowana” przy innej aplikacji z sebban-apps.** Sprawdź `id` i `scope` w manifestach obu aplikacji (patrz „Tożsamość PWA”). Każda musi mieć inny `id`, a `scope` ograniczony do swojego katalogu.

**Po przeniesieniu repozytorium nie ma danych.** Dane są przypisane do adresu strony. Na starym adresie zrób eksport kopii (jeśli jeszcze działa), a na nowym „Przywróć z pliku”. Starą instalację usuń z ekranu głównego.

**Strona po publikacji się nie zmienia.** Sprawdź status workflow w zakładce Actions. Zainstalowana aplikacja może trzymać starą wersję w pamięci podręcznej, pomaga odświeżenie albo ponowne otwarcie.

**Nowa ikona nie pojawia się na telefonie.** System zapamiętuje ikonę przy instalacji. Usuń aplikację z ekranu głównego i dodaj ją ponownie (najpierw zrób eksport kopii).

**Dane zniknęły.** Najczęściej przez wyczyszczenie danych witryny albo tryb prywatny. Przywróć dane z ostatniego pliku kopii.

**Brak opcji „połącz z plikiem na dysku”.** Przeglądarka nie obsługuje File System Access API (np. Firefox, Safari, telefony). Opcja jest wtedy ukryta.

**Ikona kalendarza nie otwiera wyboru daty.** Starsze przeglądarki nie mają `showPicker()`. Aplikacja próbuje wtedy kliknąć ukryte pole daty. Zawsze działa też przechodzenie strzałkami i „Dziś”.

## Wersjonowanie i aktualizacje

- Zmiany wprowadzaj w `kalendarz-nawykow.html` i wypychaj na `main`, publikacja odbywa się automatycznie.
- Nie zmieniaj `id` w `manifest.json` (patrz „Tożsamość PWA”).
- Zmiana struktury danych wymaga podniesienia `BACKUP_VERSION` i obsługi starszych plików w `sanitizeBackupPayload`, tak żeby stare kopie dalej się wczytywały.
- Po każdej zmianie funkcji zaktualizuj `README.md` i ten plik.
