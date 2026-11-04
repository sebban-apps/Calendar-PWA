# Kalendarz nawyków

Prosta aplikacja do zapisywania codziennych nawyków w formie kalendarza. Każdy wpis ma kategorię (np. plastry, witaminy, kosmetyki, sport), opcjonalną godzinę, podtytuł i notatkę. Wpisy mogą się powtarzać co tydzień w wybrane dni.

Aplikacja działa w przeglądarce na telefonie i komputerze i można ją zainstalować jak zwykłą aplikację.

**Adres** https://sebban-apps.github.io/Calendar-PWA/

## Jak wygląda ekran

- **Nagłówek.** Dzisiejsza data, pole wyszukiwania, filtr ulubionych (gwiazdka), przycisk ustawień i przełącznik motywu.
- **Kalendarz.** Miesiąc z kolorowymi kropkami kategorii. Pod nazwą miesiąca jest liczba wpisów, przycisk „Dziś” i ikona kalendarza do skoku na dowolny dzień. Na dole są kategorie, które można ukrywać i pokazywać.
- **Wybrany dzień.** Lista wpisów z tego dnia i ich liczba.
- **Dodaj wpis.** Formularz dla wybranego dnia.
- **Ustawienia** (przycisk z suwakami w nagłówku). Zarządzanie kategoriami i kopia zapasowa.

Na telefonie wszystko jest w jednej kolumnie. Na komputerze kalendarz jest po lewej i zostaje na miejscu przy przewijaniu, a wpisy i formularz są po prawej.

## Co potrafi

- **Wpisy na wybrany dzień.** Kategoria, godzina, podtytuł i szczegóły. Każdy wpis można oznaczyć gwiazdką, edytować albo usunąć.
- **Wpisy powtarzalne.** Zaznacz „powtarzaj co tydzień” i wybierz dni tygodnia. Aplikacja tworzy wpisy na najbliższe 12 tygodni i dopisuje kolejne przy każdym otwarciu.
- **Wyszukiwanie.** Przeszukuje podtytuły, notatki i nazwy kategorii. Wyniki są podzielone na „Do dziś” (od najnowszych) i „Zaplanowane” (od najbliższych).
- **Ulubione.** Gwiazdka w nagłówku pokazuje tylko wpisy oznaczone gwiazdką.
- **Przejście do dnia z wyników.** Kliknięcie daty przy wyniku otwiera ten dzień w kalendarzu.
- **Ukrywanie kategorii.** Kliknięcie kategorii pod kalendarzem chowa ją z kalendarza i list (jest wtedy przekreślona). „wszystkie” i „żadna” przełączają wszystkie naraz.
- **Zarządzanie kategoriami.** Dodawanie, zmiana nazwy, wybór koloru (12 gotowych albo własny) i usuwanie z wyborem, co zrobić z wpisami.
- **Jasny i ciemny motyw.** Domyślnie taki jak w systemie. Wybór z przycisku w nagłówku zostaje zapamiętany.
- **Kopia zapasowa.** Eksport i przywracanie danych z pliku oraz automatyczna kopia co 7 dni.

## Jak korzystać

### Dodawanie wpisu

1. Kliknij dzień w kalendarzu (na start wybrany jest dzisiejszy).
2. W karcie „Dodaj wpis” wybierz kategorię.
3. Opcjonalnie wpisz podtytuł, godzinę (np. `8:00` albo `8.00`) i szczegóły.
4. Kliknij „Dodaj wpis”. Na dole ekranu pojawi się krótkie potwierdzenie.

**Nowa kategoria od razu w formularzu.** Kliknij „+ nowa”, wpisz nazwę i naciśnij Enter albo „Dodaj”. Escape anuluje.

### Wpis powtarzalny

1. Zaznacz „powtarzaj co tydzień”.
2. Wybierz dni tygodnia (Pn do Nd). „cały tydzień” zaznacza wszystkie, „wyczyść” odznacza.
3. Kliknij „Dodaj wpis”.

Wpisy z serii mają ikonę dwóch strzałek obok kategorii.

### Edycja i usuwanie

- Ikona ołówka otwiera edycję. Można zmienić kategorię, godzinę, podtytuł, szczegóły i powtarzanie. Zmiana dni powtarzania obowiązuje od edytowanego dnia w przód, wcześniejsze wpisy serii zostają bez zmian.
- Ikona kosza pyta o potwierdzenie. Przy wpisie z serii można usunąć tylko ten dzień albo całą serię od tej daty.

### Szukanie

Wpisz tekst w polu w nagłówku. Na telefonie wyniki zastępują kalendarz, żeby były od razu pod polem. „wyczyść” albo klawisz Escape wraca do kalendarza.

### Ustawienia

Przycisk z suwakami w nagłówku albo „zarządzaj” pod kalendarzem otwiera okno ustawień. Zamyka się krzyżykiem, klawiszem Escape albo kliknięciem obok okna.

## Instalacja

Otwórz https://sebban-apps.github.io/Calendar-PWA/ i

- **Android (Chrome)** wybierz menu z trzema kropkami, potem „Dodaj do ekranu głównego” albo „Zainstaluj aplikację”,
- **iPhone (Safari)** wybierz przycisk udostępniania, potem „Do ekranu początkowego”,
- **komputer (Chrome, Edge)** kliknij ikonę instalacji na końcu paska adresu.

Kalendarz instaluje się jako osobna aplikacja, niezależnie od Dziennika nauki i Dziennika treningowego z tego samego adresu sebban-apps.github.io.

## Gdzie są zapisane dane

Wszystkie dane są zapisywane **tylko w tej przeglądarce na tym urządzeniu**. Nic nie trafia na serwer i nie ma kont użytkowników.

To oznacza, że

- dane z telefonu nie pojawią się same na komputerze (można je przenieść plikiem kopii),
- wyczyszczenie danych przeglądarki albo odinstalowanie aplikacji usuwa wpisy,
- tryb prywatny nie zachowuje danych po zamknięciu karty,
- dane są przypisane do adresu strony, więc po zmianie adresu trzeba je przenieść plikiem kopii.

### Kopia zapasowa (Ustawienia → Kopia zapasowa)

- **„Eksportuj kopię”** zapisuje plik `nawyki-kopia-RRRR-MM-DD.json`. Na telefonie może otworzyć się okno udostępniania.
- **„Przywróć z pliku”** najpierw pokazuje, co jest w pliku (data kopii, liczba wpisów i kategorii). Dopiero „Zastąp dane” wczytuje plik. Uwaga, to zastępuje wszystkie obecne dane.
- Plik kopii z innej aplikacji (np. z Dziennika nauki) zostanie odrzucony, dane się nie zmienią.
- **Automatyczna kopia.** Jeśli od ostatniej kopii minęło 7 dni, aplikacja przy otwarciu sama pobierze plik kopii i pokaże o tym krótki komunikat.
- **„połącz z plikiem na dysku”** (tylko Chrome i Edge na komputerze). Każda zmiana jest od razu zapisywana do wybranego pliku, dopóki karta jest otwarta.

Pliki kopii z wcześniejszych wersji aplikacji wczytują się bez zmian, a nowe pliki działają też w starszej wersji. Motyw (jasny lub ciemny) nie jest częścią kopii.

## Internet i tryb offline

Do otwarcia strony potrzebne jest połączenie z internetem. Samo korzystanie i zapisywanie danych odbywa się lokalnie. Bez internetu czcionki mogą wyglądać nieco inaczej.

## Ograniczenia

- Brak synchronizacji między urządzeniami.
- Brak przypomnień i powiadomień.
- Godzinę trzeba wpisać z minutami (`8:00`, nie `8`).
- Wpisy powtarzalne są generowane z wyprzedzeniem 12 tygodni, kolejne pojawiają się po otwarciu aplikacji.
