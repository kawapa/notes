# Inne ciekawe

Dla kompilatora w C++ nie ma czegoś takiego jak metoda klasy.

```cpp
Class Entity {
    double calculate(double number);
};

// to tak na prawdę:

double calculate(Entity* this, double number);
```

---

W pętli `for` można zainicjować więcej niż jedną zmienną, ale wszystkie będą takiego samego typu jak ta pierwsza:

```cpp
for (size_t i = 0, j = 5; ... ; ... ) { }
```

---

Można porównać dwie tablice `std::array` poprzez `a == b`, nie działa to z tablicami typu C

---

Dla typów wbudowanych nie ma sensu przekazywac jako `const &` bo najczesciej referencja zajmuje tyle samo miejsca lub więcej

---

`Range loop`
* Działa z kontenerami które mają iteratory `begin/end()`
* Nie działa w funkcjach do których dostarczany jest (jako argument) wskaźnik do pierwszego elementu

---

Operator `post-` i `pre-`dekrementacji.

```cpp
// operator predekrementacji
A& operator--() {
    x--;
    return *this;
}

// operator postdekrementacji
A operator--(int) {
    A tmp(*this);
    operator--(); // wywołanie predekrementacji
    return tmp;
}
```

---

Tylko podczas tworzenia tablicy można nadaź jej wartości listą inicjalizacyjną. Później trzeba użyć pętli.

---

Deklaracja wyprzedzająca

```cpp
class B;

class A {
public:
    B b1;
};

class B {

};
```

___

Sprawdzenie maksymalnej liczby jaką może przechować dany typ:

```cpp
#include <climits>

std::cout << INT_MAX;
```

---

Wyświetlenie liczby w formacie szesnastkowym, ósemkowym, binarny i decymalnym:

```cpp
#include <bitset>

int number = 149;	
std::cout << std::hex << number << std::endl;
std::cout << std::oct << number << std::endl;
std::cout << std::bitset<8>(number) << std::endl;
std::cout << std::dec << number;
```

---

Wstawianie znaku specjalnego do `std::cout`:

```cpp
std::cout << "\"";	// wyświetli "
```

