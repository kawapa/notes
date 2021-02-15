# Smart pointers

RAII to jest wzorzec projektowy

* RAW pointery nie powinny być używane bo w przypadku kiedy program lub jakakolwiek funkcja rzuci wyjątek po utworzeniu obiektu (za pomocą `new`) to nigdy nie dojdziemy do miejsca w kodzie gdzie mamy `delete`

Niebezpieczeństwa samodzielnego zarządzania pamięcia:
* Podwójne zwolnienie tej samej pamięci za pomocą `delete`

Właściciel zaalokowanych danych = właściciel to ten co odpowiada zajego usunięcie


Przy przekazywaniu RAW pointerów do funkcji jest niebezpieczeństwo że one zwalnią pamięć na którą wskazuje ten wskaźnik.

Smart pointer to obiekt, który zachowuje się jak wskaźnik:
* Operator * wysłuskuje dane
* Operator -> zwraca referencję na dane
* Można przypisać do niego nullptr
* Można sprawdzić czy zawiera nie-nullową wartość (`if (ptr) { ... }`)

W C++11 co prawda nie ma std::make_unique ale możemy sobie zami stworzy szablon wariadyczny, który będzie przyjmowal w konstruktorze arguemnty i zwróci utworzony std::unique_ptr


Tworzenie tablicy używając

```cpp
std::unique_ptr<X[]> tab = std::make_unique<X[]>(5);
tab[1] = ...
```

```cpp
ptr.reset(new X);   // zwolnienie obecnej pamieci i przypisanie do nowego elementu

ptr.reset();    // zwolnienie pamięci i przypisanie nullptr
```


Wyciąganie wskaźnika z rezygnacja z wlasnosci

```cpp
X* raw = ptr.release();
```

Wyciągnięcie gołego wskaźnika z **utrzymaniem prawa własności**
```cpp
X* raw = ptr.get();
```

std::move nie przenosi niczego tylko zamienia wartosc ktora dostaje na prawostronna referencje. Cała magia dzieje się w unique_ptr