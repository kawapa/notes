# Keywords

[`decltype`](#decltype), [`static`](#static), [`default`](#default), [`constexpr`](#constexpr)[`delete`](#delete), [`final`](#final), [`override`](#override), [`noexcept`](#noexcept),[`volatile`](#volatile),

explicit

## `volatile`

Informuje kompilator, że wartość zmiennej może zmienić się *z zewnątrz* dlatego kompilator powinien zrezygnować z agresywnej optymalizacji i przy każdym odwołaniu do tej zmiennej wczytać nową wartość z komórki pamięci.

## `decltype`

Umożliwia kompilatorowi określenie zadeklarowanego typu dla podanego jako argument obiektu lub wyrażenia.

```cpp
std::map<std::string, float> coll;

decltype(coll) coll2;               // coll2 has type of coll
decltype(coll)::value_type val;     // val has type float
```

## `static`

 przed zmiennymi w funkcji lub w klasie
W przypadku zmiennej `static` wewnątrz funkcji, pamięć dla zmiennej jest zaalokowana na cały okres uruchomienia programu. Zmienna, mimo że jest zadeklarowana wewnątrz funkcji (i inkrementowana), będzie pamiętać swój stan nawet po kilku uruchomieniach funkcji.

```cpp
void demo() {  
    static int count = 0;           // zmienna nie jest deklarowana na nowo
    std::cout << count << " ";      // przy każdym wywołaniu funkcji
    count++;
}

int main() {
    for (int i=0; i<5; i++)
        demo();
    return 0;
}
```
Output: `0 1 2 3 4`

### `static` w kontekście obiektów i metod klasy

W przypadku zmiennej `static` wewnątrz klasy, zmienna ta jest wspólna dla wszystkich obiektów danej klasy. **Zmienna ta nie może być inicjalizowana za pomocą konstruktora.**

```cpp
class GfG {
public:
    static int i;
    GfG() {};
};

int GfG::i = 1;             // musi być zadeklarowana globalnie

int main() {
    GfG obj;
    std::cout << obj.i;     // lub GfG::i
}
```
Output: `1`

Zasięg obiektu `static`:

```cpp
class GfG {
    int i = 0;
    public:
    GfG() {
        i = 0;
        std::cout << "Inside Constructor\n";
    }
    ~GfG() { std::cout << "Inside Destructor\n"; }
};

int main() {
    int x = 0;
    if (x == 0) {
        static GfG obj;
        std::cout << obj.i;
    }
    std::cout << "End of main\n";
}
```

Output:<br>
`Inside Constructor`<br>
`End of main`<br>
`Inside Destructor`

Można wywołać metodę `static` bez tworzenia obiektu poprzez `GfG::printMsg()`. **Metody statyczne mają dostęp tylko do statycznych elementów klasy, nie można używać wskaźnika `this`.**

```cpp
class GfG {
public:
    static void printMsg() { std::cout << "Welcome to GfG!"; }
};

int main() {
    GfG::printMsg();
}
```
Output:

> `Welcome to GfG!`


## `constexpr`

Wyróżniamy:

* Stałe (zmienne)
    * Różni się od `const` (zmienna stała, której nie można zmieniać) tym, że wartość `constexpr` **dodatkowo** może zostać użyta do obliczeń dokonywanych w momencie kompilacji
* Funkcje stałe
    * Jeśli funkcja `constexpr` przyjmuje parametry które są znane w trakcie kompilacji, to kompilator zamiast kodu funkcji wstawi od razu `return X` - **to optymalizacja**

## `default`

* Definiując którąkolwiek z poniższych funkcji samodzielnie, kompilator nie wygeneruje reszty automatycznie. Trzeba napisać ich deklaracje i opatrzyć słowem kluczowym `default`.
    * Destruktor
    * Konstruktor domyślny
    * Konstruktor kopiujący (`Entity(const Entity& other)`)
    * Konstruktor przenoszący (`Entity(Entity&& object)`)
    * Operator przypisania kopiujący (`Entity& operator=(const Entity& other)`)
    * Operator przypisania przenoszący (`Entity& operator=(Entity&& object)`)

## `delete`

* Użycie za funkcją oznacza że użycie danej metody jest zablokowane
    * Działą zarówno dla metod jak i funkcji
* W kontekście zmiennych i tablic alokowanych na stosie oznacza zwolnienie pamięci

```cpp
void a(int a) { std::cout << a; }
void a(double d) = delete;

int main() {
    a(1);           // OK
    short s = 3;
    a(s);           // OK, konwersja short->int
    a(3.12);        // ERROR, próba skorzystania z funkcji delete
}
```

## `final`

* Przy klasie/strukturze (`class Entity final`) oznacza, że danej z danej klasy nie można dziedziczyć
* Przy metodzie w klasie oznacza, że danej metody od danego miejsca w hierarchii dziedziczenia nie można przesłaniać w klasach pochodnych

## `override`

* Opcjonalne ale zalecane
* Każe sprawdzić kompilatorowi czy funkcja z klasy bazowej jest przeciążona. Jeśli nie, kod się nie skompiluje.
* Przy np. błędzie (literówce) w nazwie w funkcji pochodnej tworzy się nowa funkcja

## `noexcept`

```cpp
void boo() noexcept;
```

* Oznacza, że dana funkcja nie rzuca wyjątków
* Kompilator może sobie pozwolić na dodatkowe optymalizacje jeśli funkcja oznaczona jest jako `noexcept`
    * Jeśli taka funkcja mimo wszystko rzuci wyjątek - zostanie zawołane `std::terminate`
* Domyślnie powinno się oznaczać `noexcept`
    * Operacje przenoszenia (konstruktor przenoszący i operator przypisania przenoszący)
    * Operacje wymiany (`swap`)
* Można sprawdzić czy dana funkcja jest noexcept

```cpp
std::cout << noexcept(boo());   // false
```

## `explicit`

* Zabrania niejawnej konwersji i przy jednoargumentowych konstruktorach.

```cpp
class Foo {
    int number_;

public:
    explicit Foo(int number)
        : number_(number) { }
    void printNum(){ std::cout << number_ << '\n'; }
};

void bar(Foo foo) {
    foo.printNum();
}

int main() {
    Foo foo(5);
    bar(foo);

    bar(10); // to już nie zadziała bo jest explicit
}
```