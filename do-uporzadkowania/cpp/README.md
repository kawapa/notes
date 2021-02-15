- [1. Szablony funkcji](#1-szablony-funkcji)
  - [Wypisywanie typu](#wypisywanie-typu)
- [2. Szablony klas](#2-szablony-klas)
  - [Specjalizacje](#specjalizacje)
  - [Częściowa specjalizacja](#częściowa-specjalizacja)
- [3. Szablony wariadyczne](#3-szablony-wariadyczne)

## 1. Szablony funkcji

* Rozwijanie i dedukowanie szablonów dzieje się na etapie kompilacji.
* Typy błędów związanych z szablonami są długie i nieczytelne

Przykład:

```cpp
#include <complex>

template <typename T>       // lub <class T> lub <int> (np. std::array)
T add(T first, T second) {
    return first + second;
}

int main() {
    auto result1 = add<int>(4, 5);                             // int
    auto result2 = add<double>(4.0, 5.0);                      // double
    auto result3 = add<std::complex<int>>({1, 2}, {2, 3});     // complex<int>
}
```

Wbudowana automatyczna dedukcja typów (z pewnymi wyjątkami):

```cpp
int main() {
    auto result1 = add<int>(4,5);           // OK
    auto result2 = add(4,5);                // OK, automatyczna dedukcja typów (int)
    auto result3 = add({1, 2}, {2, 3});     // ERROR, { } wskazuje na listę inicjalizacyjną
}
```

### Wypisywanie typu

```cpp
#include <typeinfo>

template <class T>
void doNothing() {      // typu T nie ma liście argumentów!
    T value;
    std::cout << typeid(value).name();
}

int main() {
    doNothing();        // ERROR
                        // typu T nie ma na liście argumentów
                        // trzeba go podać jawnie
    
    doNothing<int>();   // OK
}
```

## 2. Szablony klas

```cpp
template <typename T>
class SomeClass {
public:
    T getValue() { retrun value; }

private:
    T value;
};

int main() {
    SomeClass<int> sc;
    std::cout << sc.getValue();
}
```

Do C++14 włącznie **nie ma automatycznej dedukcji typu** dla szablonów klas! Trzeba podać jawnie.
**Od C++17** kompilator sam wydedukuje typ **w oparciu o typ danych który zostanie podany do konstruktora**.

```cpp
template <class T = float>
class Entity {
public:
    T name_;
    Entity() : name_{} {}
    Entity(T name) : name_(name) { }
};

int main() {
    Entity entity1{1};      // Entity<int>
    Entity entity2{2.55};   // Entity<float>
    Entity entity3;         // Entity<float>

    // Przed C++17, nawet jeśli podany był domyślny typ, trzeba byłoby zastosować konstrukcję Entity<> entity3
}
```

### Specjalizacje

Zarówno szablony funkcji i klas można specjalizować. Specjalizacje to inna implementacja funkcji/klasy w zależności od typu:
* jaki prześlemy do funkcji
* jakim zainicjalizujemy klasę

```cpp
#include <iostream>

template <class T = float>
struct is_int {
    static const bool value = false;
};

template <>             // specjalizacja
struct is_int<int> {    // specjalizacja dla int
    static const bool value = true;
};

int main() {
    std::cout << "Char: " << is_int<char>::value << std::endl;  // 0
    std::cout << "Int: " << is_int<int>::value << std::endl;    // 1
    std::cout << "Float: " << is_int<>::value << std::endl;     // 0, wymagany zapis is_int<>
}
```

### Częściowa specjalizacja

W przeciwieństwie do pełnej specjalizacji szablonów częściowa specjalizacja szablonów pozwala wprowadzić szablon z poprawionymi niektórymi argumentami istniejącego szablonu. 
Częściowa specjalizacja szablonów jest dostępna tylko dla klasy / struktur szablonów.

Po utworzeniu wystąpienia częściowo wyspecjalizowanego szablonu wybierana jest najbardziej odpowiednia specjalizacja. Na przykład zdefiniujmy szablon i dwie częściowe specjalizacje:

```cpp
template<typename T, typename U, typename V>
struct S {
    static void foo() {
        std::cout << "General case\n";
    }
};

template<typename U, typename V>
struct S<int, U, V> {
    static void foo() {
        std::cout << "T = int\n";
    }
};

template<typename V>
struct S<int, double, V> {
    static void foo() {
        std::cout << "T = int, U = double\n";
    }
};
```

Teraz następujące połączenia:

```cpp
S<std::string, int, double>::foo();
S<int, float, std::string>::foo();
S<int, double, std::string>::foo();
```

Output:

> `General case`<br>
> `T = int`<br>
> `T = int, U = double`


## 3. Szablony wariadyczne

Nieprzerobione...