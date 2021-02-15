# Exceptions

Zazwyczaj obsługę błędów robi się na wyjątkach

* Wymaga `#include <stdexcept>`

```cpp
int foo() {
    inst1();
    auto v = fun();
    if (v < 0) {
//        return -1;
    }
    return v;
}
```

Przy sprawdzeniu warunku, zamiast zwracać magiczną wartość (`return -1`), można rzucić wyjątek

```cpp
int foo() {
    inst1();
    auto v = fun();
    if (v < 0) {
        throw std::logic_error("KOMUNIKAT");
    }
    return v;
}
```

Wyjątek trzeba później złapać, robi się to zwykle w `main()`.
```cpp
int main() {

    try {
        foo();
    } catch (const std::logic_error& le) {
        std::cerr << le.what();
        // exit(-1);
        // można też ubić binarkę
    }
}
```

* Wszystkie wyjątki które dziedziczą po `std::exception` mają metodę `what()` która zwaraca dokładnie to co wpisaliśmy w nawias).



TWORZENIE Klas dziedziczacych po wyjatkach z biblioteki standardowej.

Tworzymy nową klase ktora dziedziczy po konkrentym wyjatku
https://en.cppreference.com/w/cpp/header/stdexcept

Uwaga! Trzeba wywolac (w liscie inicjalizacyjnej konsturktora) konstruktor klasy bazowej!


```cpp
class InvalidCoordinates : public std::invalid_argument { 
    InvalidCOordinates() : std::invalid_argument("Please provide proper coordinates) { }
};

class InsufficientCargo: public std::logic_error {
    InsufficientCargo() : std::logic_error("You dont hhave enough free space") { }
}
```

ZŁE PRAKTYKI

Rzucanie wszystkim, intem stringiem
```cpp
throw 'e';
```

To nie ejst dobre bo nie ma np. e.what(). Uniemozliwia to poprawna obsluge bledów.