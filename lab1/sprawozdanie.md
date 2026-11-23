# Lab 1 — pierwsze uruchomienie projektu z agentem

[Repozytorium GitHub](https://github.com/kondziuk3/agenci-ai-laboratoria)

Aplikacja i ustawienia — Codex Desktop · `gpt-6.2-sol` · `xhigh` (oznaczenia z zapisu sesji).

## 1. Wynik, zakres i sposób odbioru

Celem było wygenerowanie `results/exercise-summary.json` na podstawie `data/exercise.csv` i sprawdzenie działania gotowym testem. W pierwszym etapie użyto oryginalnych danych, kodu i testów z paczki. Zakres drugiego etapu obejmował zmianę jednej etykiety i ponowne wygenerowanie raportu. Do odbioru potrzebne były polecenia i ich wyniki, ścieżki wejścia i wyjścia oraz treść raportu po każdym uruchomieniu.

W CSV `message_id` oznacza identyfikator wiadomości, `text` zawiera jej temat i treść, a `label` jest przypisaną wcześniej etykietą. Przykładowy rekord `exercise-001` początkowo miał etykietę `ham`. Program zlicza etykiety zapisane w danych.

## 2. Jak powstał raport

Model wybrał polecenie, a aplikacja Codex przekazała je do narzędzia terminalowego. Na lokalnym komputerze z Windows PowerShell uruchomił Pythona 3.15.0, który wykonał skrypt z folderu `project/`. Narzędzie zwróciło modelowi wynik procesu i treść raportu odczytaną z dysku.

```powershell
py -3 describe_data.py --data data/exercise.csv --out results/exercise-summary.json
```

Oba uruchomienia zakończyły się kodem 0. Pole `input_file` pokazało pełną ścieżkę do podanego CSV. Pole `input_sha256` porównano z sumą obliczoną bezpośrednio z odpowiedniej wersji CSV. [Końcowy raport exercise-summary.json](exercise-summary.json) obejmuje 8 wiadomości, w tym 4 z etykietą `ham` i 4 z etykietą `spam`.

## 3. Wyjaśniony fragment i zmieniony przykład

Fragment to `project/README.md`, sekcja *Environment and first run*, krok 3. Opcja `--data` wskazuje plik, z którego program bierze wiadomości. Opcja `--out` określa nazwę i folder zapisu podsumowania. Ścieżki w poleceniu liczą się od folderu `project/`.

W przykładzie zmieniono `--out` z `results/exercise-summary.json` na `results/proba.json`. Przed powtórną próbą na kopii projektu zapisałem własne wyjaśnienie.

> `--data data/exercise.csv` oznacza, że program odczytuje dane z pliku `data/exercise.csv`, a `--out results/proba.json` oznacza, że zapisuje wynik do pliku `results/proba.json`.

Agent uruchomił oba warianty na tej samej kopii CSV. W obu plikach znalazł tę samą ścieżkę wejściową i identyczne podsumowanie (8 wiadomości, 5 `ham` i 3 `spam`). Wynik potwierdził, że zmiana `--out` w tym przykładzie wskazuje inny plik zapisu, a liczebności wynikają z danych wskazanych w `--data`.

## 4. Gotowy test

```powershell
py -3 -m unittest discover -s tests -v
```

Wynik to `Ran 1 test`, `OK` i kod zakończenia 0. Test użył `tests/fixtures/messages.csv`, czyli trzech sztucznych wiadomości. Sprawdził poprawne zakończenie procesu, zapis i odczyt raportu, ścieżkę pliku wejściowego oraz znane liczebności (3 wiadomości, 2 `ham` i 1 `spam`). Raport testowy powstał w osobnym katalogu tymczasowym.

## 5. Zmiana danych i aktualizacja raportu

W rekordzie `exercise-001` zmieniono etykietę z `ham` na `spam`. Po edycji wcześniejszy JSON nadal opisywał poprzednią wersję wejścia. Przed ponownym uruchomieniem agent przewidział przesunięcie jednej wiadomości z `ham` do `spam` przy tej samej łącznej liczbie wiadomości. Najpierw porównano sumę kontrolną zapisaną w starym raporcie z sumą aktualnego CSV. Następnie uruchomiono skrypt, odczytano nowy JSON i sprawdzono jego sumę kontrolną oraz liczebności.

| Wersja raportu | `ham` | `spam` | Łącznie |
|---|---:|---:|---:|
| Przed zmianą | 5 | 3 | 8 |
| Po ponownym uruchomieniu | 4 | 4 | 8 |

Po pierwszym przebiegu, przed powtórną próbą na kopii projektu, zapisałem własną odpowiedź.

> Nowy raport pokaże 4 wiadomości ham, 4 wiadomości spam i łącznie 8 wiadomości. Tak, trzeba ponownie uruchomić skrypt, aby zaktualizować raport.

Na kopii wcześniejszy raport po samej edycji nadal pokazywał 5 `ham` i 3 `spam`. Po ponownym uruchomieniu pokazał przewidziane 4 `ham`, 4 `spam` i 8 wiadomości łącznie. Raporty z obu przebiegów, moja odpowiedź i wyniki poleceń są w [zapisie sesji Codexa](sesje/sesja-1.jsonl).

W plikach przekazywanych na GitHub nazwę lokalnego użytkownika Windows zastąpiono przez `USER`. W zapisie sesji usunięto także identyfikator śledzący z URL i nazwy lokalnych plików sesji. Zapis obejmuje rozmowę, wywołania narzędzi i ich wyniki. Pominięto wewnętrzne metadane aplikacji.
