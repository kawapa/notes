# Rzutowanie

Rzutowanie - zamierzona zmiana typu obiektu.

1. [Rzutowanie w stylu C](#1-rzutowanie-w-stylu-c)
2. [`static_cast`](#2-static_cast)
3. [`const_cast`](#3-const_cast)
4. [`reinterpret_cast`](#4-reinterpret_cast)
5. [`dynamic_cast`](#5-dynamic_cast)

## 1. Rzutowanie w stylu C

```cpp
T objekt = (T) obiekt_innego_typu
// rzutowanie w stylu C

T obiekt = T(obiekt_innego_typu)
// rzutowanie w stylu funkcyjnym

// oba sposoby działają tak samo
```

Wady:
* Ciężko wypatrzeć je w kodzie
* Możliwe jest za pomocą ich dowolne rzutowanie

## 2. `static_cast`

* Rzutowanie w trakcie kompilacji
* Konwersji typów podstawowych: `int`, `double`, `char`
* Konwersji typów zdefiniowanych przez użytkownika (wymagany konstruktor przyjmujący jeden typ i zwracany inny)
* Konwersji wskaźników klas podstawowych na ich odpowiedniki klas pochodnych (w dół hierarchii) oraz odwrotnie (w górę hierarchii)

## 3. `const_cast`

* Służy do nadawania lub zdejmowania kwalifikatorów `const` i `volatile`
    * **Typ musi być ten sam!**

```cpp
int main() {
    int a = 22;
    const int* constA = &a;

    int* b = const_cast<int*>(a);
    // zdejmowanie const
    *b = -1;

    a = const_cast<const int*>(b);
    // nadawanie const
    std::cout << "a = " << *a;

    return 0;
}
```

```cpp
int main() {
    double a = -11;
    volatile double *b = &a;

    double *c = const_cast<double*>(b);
    // zdejmowanie volatile

    b = const_cast<volatile double*>(c);
    // nadawanie volatile

    return 0;
}
```

## 4. `reinterpret_cast`

Konwersje między niespokrewnionymi typami np. 
* Ze wskaźnika jednego typu na inny

```cpp
float *f = new float;
// zmienna typu float zajmuje 4 bajty pamięci

*f = 33.2;
int *i = reinterpret_cast<int*>(f);
// zmienna int zajmuje również 4 bajty pamięci

std::cout << "float: " << *f << " ---> int: " << *i << endl;
// ten sam układ bitów, ale inna interpretacja
```

Output: `float: 33.2 ---> int: 1107610829`

* Z liczby całkowitej na wskaźnik
    * Jeśli wskaźnik wskazuje na komórki pamięci jednego typu (np. `float`), a chcemy zapisać wskazywany adres do wskaźnika innego typu to należy użyć `reinterpret_cast`
    
```cpp
int address = 0x0f6a2f1;

int* wsk = reinterpret_cast<int*>(addres);
// ustawienie wskaźnika na adres
```

## 5. `dynamic_cast`

* Rzutowanie w trakcie wykonywanie programu
* Konwersji wskaźników klas podstawowych na ich odpowiedniki klas pochodnych (w dół hierarchii) oraz odwrotnie (w górę hierarchii)
* W razie niekompatybilności `dynamic_cast` zwróci:
    * NULL w przypadku rzutowania wskaźników
    * Wyjątek `bad_cast`, w przypadku rzutowania referencji

```cpp
struct Base {
    virtual ~Base(){}
};

struct Derived : public Base {};

int main() {
    Derived* d = new Derived;

    Base* b = dynamic_cast<Base*>(d);
    // rzutowanie w górę

    Derived* new_d = dynamic_cast<Derived*>(b);
    // rzutowanie w dół

    return 0;
}
```