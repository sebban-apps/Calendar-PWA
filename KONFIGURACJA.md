# Dziennik nauki, dokumentacja techniczna

## Przegląd

Aplikacja jest statyczną stroną (PWA bez service workera) w jednym pliku `dziennik-nauki.html`. HTML, CSS i JavaScript są w tym samym pliku, bez frameworków, bundlera i zależności npm. Dane trzymane są lokalnie w przeglądarce.

Interfejs korzysta z systemu wizualnego **Modern Teal Signature** (paleta teal, Manrope i Inter, jasny i ciemny motyw).

## Struktura plików

| Plik | Rola |
|---|---|
| `index.html` | Przekierowanie do `dziennik-nauki.html` (adres główny strony) |
| `dziennik-nauki.html` | Cała aplikacja |
| `manifest.json` | Manifest PWA (nazwa, ikony, kolory, `start_url`) |
| `icon-192.png`, `icon-512.png` | Ikony aplikacji |
| `README.md` | Instrukcja dla użytkownika |
| `KONFIGURACJA.md` | Ten plik |

## Wymagania

* Dowolny serwer plików statycznych lub GitHub Pages
* Nowoczesna przeglądarka (Chrome, Edge, Firefox, Safari z ostatnich lat)
* Brak zmiennych środowiskowych, kluczy API i bazy danych

## Uruchomienie lokalne

Plik można otworzyć bezpośrednio (`file://`), ale manifest i instalacja działają tylko przez HTTP.

```bash
cd sciezka/do/projektu
python3 -m http.server 8000
# otwórz http://localhost:8000/
```

## Publikacja na GitHub Pages

1. Wrzuć wszystkie pliki do katalogu głównego repozytorium.
2. W repozytorium wejdź w **Settings → Pages**, wybierz gałąź `main` i folder `/ (root)`.
3. Strona będzie dostępna pod `https://<użytkownik>.github.io/<repozytorium>/`.

Ścieżki w `manifest.json` są względne (`./`), więc aplikacja działa także w podkatalogu.

## Usługi zewnętrzne

Jedyną zależnością sieciową są czcionki **Google Fonts** (Inter i Manrope), ładowane w `<head>`. Gdy są niedostępne, przeglądarka używa czcionki systemowej i aplikacja działa dalej.

## Przechowywanie danych

Warstwa `Store` w skrypcie wybiera magazyn w tej kolejności

1. `window.storage`, jeśli istnieje (środowisko artefaktów Claude)
2. `localStorage` przeglądarki, z prefiksem kluczy `dziennik-nauki:`
3. pamięć operacyjna (`Map`), gdy `localStorage` jest zablokowany. Dane znikają po zamknięciu, a aplikacja pokazuje ostrzeżenie

Klucze i format

| Klucz | Zawartość |
|---|---|
| `sessions` | tablica `{ id, date (ISO), subject, minutes, note, favorite }` |
| `categories` | tablica nazw kategorii |
| `presets` | tablica skrótów timera w minutach (1 do 180) |
| `dayplans` | obiekt `{ "RRRR-MM-DD": [ { id, time, text, done } ] }` |
| `theme` | `light` lub `dark`, zapisywany bezpośrednio w `localStorage` (poza `Store`), czytany przed pierwszym malowaniem strony |

Każdy odczyt przechodzi przez funkcje strażnicze (`asArray`, `asPlainObject`, filtry w `load*`), które odrzucają uszkodzone dane.

### Plik kopii zapasowej

```json
{
  "app": "dziennik-nauki",
  "version": 1,
  "exportedAt": "2026-11-03T12:00:00.000Z",
  "sessions": [],
  "categories": ["SQL", "Python", "Angielski", "Inne"],
  "presets": [20, 30, 45],
  "dayplans": {}
}
```

Import przechodzi przez `sanitizeBackupPayload` i zastępuje wszystkie cztery klucze danych. Przy zmianie formatu podnieś `BACKUP_VERSION` i obsłuż starsze wersje w tej funkcji.

## Najważniejsze parametry w kodzie

| Stała | Wartość | Znaczenie |
|---|---|---|
| `DEFAULT_CATEGORIES` | SQL, Python, Angielski, Inne | Kategorie startowe |
| `DEFAULT_PRESETS` | 45, 20, 30 | Skróty startowe |
| `parseMinutes(..., 180)` | 180 | Limit timera |
| `parseMinutes(..., 1440)` | 1440 | Limit wpisu ręcznego i edycji |
| `BACKUP_VERSION` | 1 | Wersja formatu kopii |

Etykiety „Techniczna · 45” i „Angielski · 20” są przypisane w `renderPresets()`.

## Timer

Odliczanie opiera się na znaczniku końca (`T.endAt`), a nie na liczeniu taktów `setInterval`. Dzięki temu czas jest poprawny także wtedy, gdy przeglądarka spowalnia kartę w tle. Interwał co 250 ms odświeża widok tylko przy zmianie sekundy, a zdarzenie `visibilitychange` wymusza przeliczenie po powrocie do karty. Po naturalnym zakończeniu wywoływane jest `navigator.vibrate` (jeśli dostępne).

## System wizualny

Wszystkie kolory, promienie, odstępy, czcionki i czasy animacji są tokenami CSS w `:root`.

* Motyw ciemny jest zdefiniowany dla `:root[data-theme="dark"]` oraz dla `prefers-color-scheme: dark`, gdy użytkownik nie wybrał jasnego.
* Kolory marki `--teal-deep`, `--teal`, `--teal-soft`, `--teal-mist`. Semantyczne `--success`, `--warning`, `--error`, `--info`.
* Komponenty używają tokenów pochodnych (`--accent`, `--accent-tint`, `--accent-text`, `--surface`, `--elevated`), więc zmiana motywu nie wymaga osobnych reguł dla komponentów.
* Promienie `--r-sm` 10px, `--r-btn` 12px, `--r-card` 16px, `--r-lg` 20px. Odstępy `--s1` do `--s7` (4 do 48 px).
* `prefers-reduced-motion` wyłącza animacje.

Okna potwierdzenia i wpisywania nazwy kategorii to własny komponent `Dialog` (zamiast `confirm()` i `prompt()`), a krótkie komunikaty obsługuje funkcja `toast()`.

## Testy

Automatycznych testów w repozytorium nie ma. Elementy mają atrybuty `data-testid`, a po starcie ustawiana jest flaga `window.__appReady = true`, co ułatwia testy np. w Playwright.

Ręczna lista kontrolna po zmianach

* start, pauza, wznowienie i zakończenie timera, zapis notatki
* dodanie kategorii przez okno dialogowe
* ręczny wpis z błędnymi i poprawnymi danymi
* edycja, ulubione, wyszukiwanie, filtr i usunięcie wpisu
* wpis z kalendarza i przełączanie miesięcy
* plan dnia, walidacja godziny, odhaczanie punktów
* eksport i import kopii
* przełączenie motywu i odświeżenie strony
* widok na szerokości 360 px i na komputerze

## Rozwiązywanie problemów

| Problem | Przyczyna i rozwiązanie |
|---|---|
| Brak opcji instalacji | Strona otwarta z `file://` albo bez HTTPS. Uruchom przez serwer lub GitHub Pages |
| Po aktualizacji widać starą wersję | Pamięć podręczna przeglądarki. Odśwież z wyczyszczeniem cache lub zamknij i otwórz aplikację |
| Dane zniknęły | Wyczyszczone dane witryny, inny adres (np. inna domena lub `localhost` zamiast Pages) albo tryb prywatny. Przywróć z kopii |
| Inne czcionki niż w projekcie | Google Fonts zablokowane. Aplikacja działa na czcionkach systemowych |
| Import zgłasza błąd | Plik nie jest JSON albo nie pochodzi z tej aplikacji |

Dane są przypisane do adresu (origin). Zmiana domeny lub nazwy repozytorium oznacza nowy, pusty magazyn, więc przed przenosinami trzeba zrobić eksport.

## Wersjonowanie i aktualizacje

* Wersja formatu danych to `BACKUP_VERSION` w skrypcie.
* Aktualizacja polega na podmianie plików na serwerze. Ponieważ nie ma service workera, użytkownik dostaje nową wersję przy kolejnym załadowaniu strony.
* Zmiany w strukturze danych powinny być wstecznie zgodne lub obsłużone migracją w funkcjach `load*`.

## Możliwe rozszerzenia

Nie są zaimplementowane.

* service worker z pamięcią podręczną dla pracy offline
* ikony typu `maskable` dla Androida
* powiadomienia systemowe o końcu sesji
