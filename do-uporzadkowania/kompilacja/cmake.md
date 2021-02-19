# Systemy budowania

## Make

* Automatyzuje proces budowania
* Plik `Makefile` **musi być bez rozszerzenia**
* Komenda `make` szuka w bieżącym katalogu pliku `Makefile` i jeśli nie ma podanego żadnego dodatkowego argumentu wykonuje pierwszą z góry recepturę


```cmake
greeter: *.cpp, *.hpp
    g++ -std=c++17 -Wall -Werror -Wextra -pedantic *.cpp

clean:
    rm greeter

.PHONY: clean
```

* Normalnie *Make* sprawdza czy plik o nazwie **NAZWA** jest nowszy niż wszystkie zależności wpisane po dwukropku. Jeśli jest plik o takiej samej nazwie jak receptura (**NAZWA**) jest nowszy to przy próbie wywołania `make NAZWA` wyświetli się `make: NAZWA is up to date.`

* Jeśli chcemy by komenda wywoływała się niezależnie od systemu plików (np. w ścieżce może istnieć plik `clean` i wtedy komenda `clean` się nie wykona!) to dodajemy ją do tzw. PHONY.

### Tworzenie zmiennych

```cmake
VARIABLE = value

targetA: dependencyA1 dependencyA2
[TAB]   command $(VARIABLE)

targetB: dependency B1
[TAB]   command &(VARIABLE)
```

## CMake

* Automatyzuje proces budowania
* Plik `CMakeLists.txt`
* CMake generuje `Makefile`

### Minimalny CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.10)
project(ProjectName)

add_executable(execName main.cpp file.cpp)
```

### Budowanie


```cmake
mkdir build      # tworzymy katalog z wynikami budowania
cd build         # wchodzimy do tego katalogu
cmake ..         # generujemy system budowania podając ścieżkę do pliku CMakeLists.txt
cmake --build .  # budujemy projekt
```

* `cmake --build` można zamienić na `make` jeśli wiemy, że na pewno generujemy Makefile
* `cmake --build .` jest uniwersalne

### Tworzenie zmiennych

```cmake
set(VARIABLE value)  # Konwencja - UPPERCASE_WITH_UNDERSCORE
```

Przykład:
```cmake
set(NAME TheGreatestProject)
```

* `project(greeter)` tworzy zmienną `PROJECT_NAME` o wartości `greeter`
* Do zmiennych odwołujemy się poprzez `${NAZWA_ZMIENNEJ}`

### Tworzenie aplikacji

```cmake
add_executable(<name> [source1] [source2 ...])
```

```cmake
add_executable(${NAME} main.cpp)
```

* W `add_executable` nie można podać `*.cpp`
* Jeśli pliki `*.hpp` są w innej lokalizacji to trzeba dodać `include_directories(inc)`
    * Można dodać więcej ścieżek `include_directories(inc1/ inc2/)

### Tworzenie bibliotek

* Biblioteka to zlepek wielu plików cpp **bez funkcji main()**. Biblioteki nie można z tego powodu uruchomić.

```cmake
add_library(${PROJECT_NAME}-lib STATIC functions.cpp modules.cpp)
```

* `STATIC` oznacza, że za każdym razem kopiuje nam tą library do binarki
* Alternatywą dla biblioteki jest utworzenie zmiennej, na przykład `SRC_LIST` zawierającej wszystkie pliki `*.cpp`

#### Linkowanie bibliotek

Biblioteki można też linkować ze sobą poprzez:

```cmake
target_link_libraries(<target> ... <item>... ...)
```

* `target` - to nazwa `add_executables`
* `<item>` - to konkretna biblioteka

Przykład:
```cmake 
add_library(lib STATIC functions.cpp modules.cpp)
add_executable(main main.cpp)
add_executable(ut tests.cpp)
target_link_libraries(main lib)
target_link_libraries(ut lib)
```

### Podsumowanie

```cmake
add_library(${PROJECT_NAME}-lib STATIC functions.cpp modules.cpp)
add_executable(${PROJECT_NAME} main.cpp functions.cpp modules.cpp)
add_executable(${PROJECT_NAME}-ut test.cpp functions.cpp modules.cpp)
```

### Dodawanie flag kompilacji

* `target_compile_option` pozwala na ustawianie różnych flag dla różnych binarek
* Flagi kompilacji można wrzucić też do zmiennej i je wrzucać do wybranych (lub wszystkich) `target_compile_options`
* Można **raz** ustawić flagę **dla wszystkich binarek** poprzez: `add_compile_options(-Werror -Wextra -Werror)`
* **Do bibliotek też należy ustawiać flagi kompilacji!**

```cmake
target_compile_options(<target> [BEFORE]
                                <INTERFACE|PUBLIC|PRIVATE> [items1...]
                                [<INTERFACE|PUBLIC|PRIVATE> [items2...]
                                ...])
```

* To co jest w nawiasach kwadratowych `[ ]` jest opcjonalne

```cmake
add_executable(${PROJECT_NAME} main.cpp)
target_compile_options(${PROJECT_NAME} PRIVATE -Wall -Wextra -Werror)
```

### Włączanie standardu C++17

```cmake
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
```

* Powyższe może nie działać dla MSVC

```cmake
set_target_properties(${PROJECT_NAME} PROPERTIES
                      CXX_STANDARD 17
                      CXX_STANDARD_REQUIRED ON)
```

```cmake
add_executable(${PROJECT_NAME} main.cpp)
target_compile_features(${PROJECT_NAME} PRIVATE cxx_std_17)
```

## CTest

* CTest to moduł CMake - aplikacja do uruchamiania testów
    * Normalnie należy uruchomić binarkę z testami
    * CTest umożliwia uruchomienie wszystkich testów jedną komendą - `ctest`

```cmake
enable_testing()
add_test(NAME <name> COMMAND <command> [<arg>...])
```

Przykład:
```cmake
enable_testing()
add_test(NAME someTests COMMAND ./${PROJECT_NAME}-ut)
```

### Uruchamianie testów z CTest

* `ctest` - uruchomienie wszystkich testów
* `ctest -V` - jw. + pokazuje też to co binarka pokazuje (output binarki)

### Budowanie w trybie Debug

* Domyślnie budowany jest tryb "Release" (bez symboli debugowania)

```cmake
cmake -DCMAKE_BUILD_TYPE=Debug ..
```


* Jeśli chcemy wspierać budowanie w trybach Debug i Release powinniśmy mieć do nich oddzielne katalogi z rezultatami budowania

```cmake
mkdir buildDebug
cd buildDebug
cmake -DCMAKE_BUILD_TYPE=Debug ..
cmake --build
```


Lokalizacja bibliotek w Linux

Headers: /usr/local/include
Libs: /usr/local/lib

```bash
cp -r ../include/gtest /usr/local/include
cp lib*.a /usr/local/lib
```