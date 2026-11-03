# Dziennik nauki

Prosta aplikacja do mierzenia czasu nauki i zapisywania krótkich notatek po każdej sesji. Działa w przeglądarce na telefonie i komputerze, a na telefonie można ją zainstalować jak zwykłą aplikację.

## Co potrafi

* **Timer** z tarczą i wskazówką, gotowymi skrótami (45, 20, 30 min) i własnym czasem od 1 do 180 minut
* **Notatka po sesji** z wyborem kategorii (SQL, Python, Angielski, Inne oraz własne)
* **Dziennik** z wyszukiwaniem, filtrem kategorii, ulubionymi, edycją i usuwaniem wpisów
* **Statystyki** z ostatnich 7 dni i łącznie, z podziałem na kategorie
* **Kalendarz** miesiąca, w którym widać dni z nauką i liczbę minut
* **Plan dnia** z listą punktów, godzinami i paskiem postępu
* **Kopia zapasowa** do pliku JSON i przywracanie z takiego pliku
* **Jasny i ciemny motyw**

## Jak korzystać

### Timer

1. Wybierz skrót albo wpisz własny czas w polu „własny czas”.
2. Naciśnij **Zacznij**. Podczas odliczania możesz dać **Pauza** i **Wznów**.
3. Gdy czas minie, telefon krótko zawibruje (jeśli to obsługuje) i pojawi się ekran notatki. Sesję można też skończyć wcześniej przyciskiem **Zakończ i zapisz**, wtedy zapisze się faktycznie przepracowany czas.
4. Wybierz kategorię, dopisz kilka słów i naciśnij **Zapisz**. **Pomiń** wraca do timera bez zapisu.

Przycisk **zapisz jako skrót** dodaje wpisany czas do listy skrótów. Pozostały czas widać też w tytule karty przeglądarki.

### Dziennik

* Pole wyszukiwania przeszukuje notatki i nazwy kategorii.
* Gwiazdka obok pola pokazuje tylko ulubione wpisy.
* Przy każdym wpisie są trzy ikony, czyli ulubione, edycja i usuwanie. Usunięcie trzeba potwierdzić.
* **+ zapisz minioną sesję** dodaje wpis z dowolną datą, godziną i długością (do 1440 minut). To samo otwiera link pod timerem.

### Kalendarz

Strzałki zmieniają miesiąc. Dzień z wpisami ma kropki i sumę minut, a dzisiejszy dzień jest obwiedziony. Po kliknięciu dnia widać jego wpisy i formularz dodania nowego wpisu na ten dzień. Ponowne kliknięcie zamyka podgląd.

### Plan dnia

Wpisz godzinę (np. `9:00`, można pominąć) i treść punktu, potem naciśnij **+** albo Enter. Kwadrat po lewej oznacza punkt jako zrobiony. Strzałki przełączają dni.

### Kategorie

Przycisk **+ nowa** przy wyborze kategorii otwiera okno, w którym wpisujesz nazwę. Nowa kategoria od razu pojawia się w filtrach i formularzach.

### Motyw

Ikona księżyca lub słońca w prawym górnym rogu przełącza motyw. Wybór jest zapamiętywany. Dopóki nic nie wybierzesz, aplikacja dopasowuje się do ustawień systemu.

## Instalacja na telefonie

Otwórz adres aplikacji w przeglądarce.

* **Android (Chrome)** menu ⋮ i **Dodaj do ekranu głównego** lub **Zainstaluj aplikację**
* **iPhone (Safari)** przycisk udostępniania i **Do ekranu początkowego**

Na komputerze wystarczy otworzyć stronę w przeglądarce.

## Gdzie są zapisane dane

Wszystkie wpisy, kategorie, skróty i plany dnia są zapisywane **tylko w tej przeglądarce na tym urządzeniu**. Nic nie jest wysyłane na serwer i nie ma konta.

Oznacza to, że

* dane z telefonu nie pojawią się same na komputerze,
* wyczyszczenie danych przeglądarki lub odinstalowanie aplikacji usuwa wpisy,
* tryb prywatny może nie zapamiętywać danych. Aplikacja ostrzega wtedy komunikatem.

Dlatego warto co jakiś czas użyć **eksportuj kopię zapasową** w zakładce Dziennik. Na telefonie plik można od razu wysłać, np. na Dysk Google. **przywróć z kopii** wczytuje taki plik i **zastępuje** obecne dane, więc aplikacja pyta przed tym o potwierdzenie. W ten sposób przeniesiesz też dane na inne urządzenie.

## Ograniczenia

* Aplikacja potrzebuje internetu przy pierwszym otwarciu i do pobrania czcionek. Nie ma trybu offline z pamięcią podręczną, bez sieci strona może się nie otworzyć.
* Timer nie wysyła powiadomień systemowych. Jeśli telefon zablokuje ekran, odliczanie liczy się poprawnie i po powrocie zobaczysz ekran notatki, ale sygnał zakończenia może nie dotrzeć.
* Statystyka „ostatnie 7 dni” liczy siedem dób wstecz od teraz, a nie tydzień kalendarzowy.
