# Kompilator

1. [Etapy kompilacji](#etapy-kompilacji)
2. [Obługa kompilatora z linii komend](#obsluga-kompilatora-z-linii-komend)

## Etapy kompilacji

**Pre-processing**
* Rozwiązywanie wszystkich dyrektyw zaczynających się od `#`
* W miejsce include wstawiana treść podanej biblioteki
* Wynik - obrobiony plik źródłowy `*.cpp`
* Flaga `-E` kończy kompilację na typ etapie

**Kompilacja**
* Właściwa kompilacja
* Dla każdego pliku `*.cpp` powstaje plik obiektowy `*.o`
* Flaga `-c` kończy kompilację na typ etapie

**Linkowanie**
* Połączenie wszystkich plików obiektowych w jeden plik wynikowy
* Wynik - gotowa binarka (`*.exe`, `*.bin`) lub biblioteka (`*.a`, `*.so`, `*.dll`)

## Obsluga kompilatora z linii komend

`g++ PLIK -o <PLIK>`<br>
`./<PLIK>`

* Przy kompilacji nie podaje się plików nagłówkowych
   * `g++ main.cpp entity.cpp`<br>
   * `g++ *.cpp`

**Flagi**
* `-Wall` - wyświetli dodatkowe ostrzeżenia typu: nigdzie nieużyta zmienna, funkcja nie-void osiągnęła koniec bez zwrócenia wartości
* `-Wextra` - dodatkowe obostrzenia poza `-Wall`
* `-Werror` - traktuje ostrzeżenia jako błędy kompilacji
* `-Wpedantic` - jeszcze bardziej rygorystyczne wymagania
* `-Wconversion` - kompilator wskaże czy nie zachodzą gdzieś niejawne konwersje

**Flagi optymalizacji**
* `-OO` - bez optymalizacji, szybsza kompilacja
* `-O1` - optymalizacja wielkości kodu oraz czasu wykonywania (poziom 1)
* `-O2` - optymalizacja wielkości kodu oraz czasu wykonywania (poziom 2)
* `-O3` - optymalizacja wielkości kodu oraz czasu wykonywania (poziom 3)
* `-OFast` - optymalizacja `-O3` z dodatkowymi optymalizacjami obliczeniowymi

**Flagi standardu C++**
* `-std=c++17`
* `-std=c++14`
* `-std=c++17`
* `-std=c++20`

**Inne flagi**
* `-g` - dodaje symbole do debugowania (`Valgrind` też ich potrzebuje)
* `-l` - linkuje z biblioteką
   * `g++ main.cpp -lpthread`
* `-E` - kończy kompilację na etapie pre-processingu
* `-c` - kończy kompilacje na zrobieniu plików obiektowych (nie robi linkowania)