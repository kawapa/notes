# Nowości w C++11 / 14 / 17

1. [Nowe elementy języka](#1-nowe-elementy-jezyka)
2. [Nowe elementy w bibliotece standardowej STL](#2-nowe-elementy-w-bibliotece-standardowej-STL)
3. [Bibliografia](#3-bibliografia)

## 1. Nowe elementy jezyka

C++11 | C++14 | C++17 |
----- | ----- | ----- |
| <ul><li>Pętla `for` po zakresie</li><li>[Lambdy](#lambdy)</li><li>[Jednolita inicjalizacja](#jednolita-inicjalizacja)</li><li>[Delegowanie konstruktorów](#delegowanie-konstruktorow)</li><li>Modyfikator `final`</li><li>Statyczne asercje</li><li>[Słowo kluczowe `auto`](#slowo-kluczowe-auto)</li><li>Słowo kluczowe `constexpr`</li><li>Słowo kluczowe `decltype`</li><li>Słowa kluczowe `virtual` oraz `override`</li><li>Słowo kluczowe `delete` (w kontekście funkcji)</li><li>[Wskaźnik `nullptr`](#wskaznik-nullptr)</li><li>Atrybuty</li><li>----</li><li>[Semantyka przenoszenia](#semantyka-przenoszenia)</li></li><li>[Szablony wariadyczne](#szablony-wariadyczne)</li><li>Referencje do r-wartości</li><li>forwarding references</li><li>[Aliasy `using`](#aliasy-using)</li><li>strongly-typed enums</li><li>user-defined literals</li><li>Instrukcja `default` (funkcje)</li><li>special member functions for move semantics</li><li>converting constructors</li><li>explicit conversion functions</li><li>inline-namespaces</li><li>Inicjalizacja niestatycznych pól klasy w miejscu ich deklaracji (nie trzeba w konstruktorze)</li><li>right angle brackets</li><li>ref-qualified member functions</li><li>trailing return types</li><li>noexcept specifier</li><li>----</li></ul> | <ul><li>[Automatyczna dedukcja typu zwracanego z funkcji](#automatyczna-dedukcja-typu-zwracanego-z-funkcji)</li><li>[Lambdy generyczne](#lambdy-generyczne)</li><li>[Wyrażenia przechwytujące lambda](#wyrazenia-przechwytujace-lambda)</li><li>[`decltype(auto)`](#decltypeauto)</li><li>[Szablony zmiennych](#szablony-zmiennych)</li><li>[Literały binarne](#literaly-binarne)</li><li>----</li><li>[Rozluźnienie ograniczeń dla funkcji `constexpr`](#rozluznienie-ograniczen-dla-funkcji-constexpr)</li><li>[Atrybut `[[deprecated]]`](#atrybut-deprecated)</li></ul> | <ul><li>[Wiązania strukturalne](#wiazania-strukturalne)</li><li>[Możliwość inicjalizacji zmiennych w konstrukcjach `if` oraz `switch`](#mozliwosc-inicjalizacji-zmiennych-w-konstrukcjach-if-oraz-switch)</li><li>[Statyczne pola wbudowane](#statyczne-pola-wbudowane)</li><li>[Automatyczna dedukcja typu szablonu klasy](#automatyczna-dedukcja-typu-szablonu-klasy)</li><li>`constexpr` if</li><li>[`constexpr` lambdy](#constexpr-lambdy)</li><li>----</li><li>[Atrybuty `fallthrough`, `nodiscard`, `maybe_unused`](#atrybuty-fallthrough-nodiscard-maybe_unused)</li><li>lambda capture this by value</li><li>----</li><li>nested namespaces</li><li>folding expressions</li><li>direct-list-initialization of enums</li><li>declaring non-type template parameters with auto</li><li>new rules for auto deduction from braced-init-list</li><li>utf-8 character literals</li></ul> |

## 2. Nowe elementy w bibliotece standardowej STL

C++11 | C++14 | C++17 |
----- | ----- | ----- |
| <ul><li>[Inteligentne wskaźniki](#-inteligentne-wskazniki)</li><li>`std::unordered_map` i `std::unordered_set`</li><li>`std::move`</li><li>`std::array`</li><li>`std::chrono`</li><li>`std::make_shared`</li><li>`std::begin/end`</li><li>`std::to_string`</li><li>----</li><li>`std::forward`</li><li>`std::thread`</li><li>`type_traits`</li><li>`std::tuple`</li><li>`std::tie`</li><li>`std::ref`</li><li>Model pamięci</li><li>`std::async`</li><ul> | <ul><li>[`std::make_unique`](#stdmake_unique)</li><li>[Literały dla `std::complex`, `std::chrono` i `std::string`](#literaly-dla-stdcomplex-stdchrono-i-std::string)</li><li>----</li><li>compile-time integer sequences</li></ul> | <ul><li>[`std::variant`](#stdvariant)</li><li>----</li><li>`std::optional`</li><li>`std::any`</li><li>`std::string_view`</li><li>`std::invoke`</li><li>`std::apply`</li><li>`std::filesystem`</li><li>`std::byte`</li><li>splicing or maps and sets</li><li>parallel algorithms</li></ul> | 

## 3. Bibliografia

* https://github.com/AnthonyCalandra/modern-cpp-features
* http://home.agh.edu.pl/~glowacki/docs/matwykl/O-o/_ProgObiekt-C++11-ZmianyWBibliot.pdf
* http://home.agh.edu.pl/~glowacki/docs/matwykl/O-o/_ProgObiekt-C++11-Rozsz.pdf
* http://home.agh.edu.pl/~glowacki/docs/matwykl/O-o/_ProgObiekt-C++14-Rozsz.pdf
* http://home.agh.edu.pl/~glowacki/docs/matwykl/O-o/_ProgObiekt-C++17-Rozsz.pdf

---

#### Lambdy

"Anonimowa" funkcja tworzona w miejscu użycia. Wyrażenie lambda w C++ składa się z:
* `[ ]` - Lista przechwytująca (capture list). Umożliwia korzystanie ze zmiennych użytych wcześniej wk odzie
* `( )` - (opcjonalne) Lista argumentów jakie jakie ma przyjmować wyrażenie lambda
* (opcjonalne atrybuty) - np. `mutable` (zmienne przechwycone przez wartość mogą być modyfikowane wewnątrz ciała wyrażenia)
* `->` (opcjonalne) typ zwracany wyrażenia lambda
* `{ }` - ciało wyrażenia lambda

#### Semantyka przenoszenia

**NIESKOŃCZONE!!!**

* Optymalizacja, żeby unikać kopii
    * Konstruktor przenoszący `Entity(Entity&& src)`
    * Operator przypisania przenoszący `Entity& operator=(Entity&& src)`
* `std::move()`
* `std::forward()`

##### Referencja do r-value

**NIESKOŃCZONE!!!**

r-value
* Obiekt tymczasowy, który nie ma nazwy i adresu
* W kolejnej linijce już nie istnieje

l-value
* Obiekt, który ma nazwę i adres
* Będzie istniał do końca zakresu


#### Jednolita inicjalizacja

* Od C++11 wszystko można inicjalizować za pomocą klamer `{ }` - np. `std::complex`, `std::vector` 
    * Wcześniej trzeba było wrzucać stosując `push_back()`
    * Wcześniej za pomocą klamer `{ }` możliwa była inicjalizacja struktur prostych POD (Plain Old Data - same pola, bez funkcji) 

**`std::initializer_list`**
* `{ }` to tak na prawdę nowy typ - `std::initializer_list`
* Można nim inicjalizować swoje typy, wystarczy napisać konstruktor przyjmujący `std::initializer_list`
* Żeby to robić, elementy na tej liście muszą być kopiowalne, nie można zainicjalizować kontenera przechowującego `std::unique_ptr`-y bo są niekopiowalne
* W przeciwieństwie do C++98, C++11 nie zezwala na niejawna konwersję typów - wszystkie elementy muszą być tego samego typu

```cpp
int array[] = {1, 2, 3, 5.5};   // C++98 OK, C++11 ERROR
```

#### Delegowanie konstruktorów

* Od C++11 można na liście inicjalizacyjnej konsturktora zawołać inny konstruktor tej samej klasy
* Ograniczenie: **nie można wtedy dodać do listy inicjalizacyjnej niczego innego**

```cpp
struct Entity {
    Entity() 
        : Entity(2),
        : y_(10) { }    // Nie można!
    Entity(int x)
        : x_(x) { }

    int x_;
    int y_;
};
```

#### Slowo kluczowe auto

* Kompilator sam ma wydedukować typ
* Przydaje się żeby nie pisać długich typów, np. `std::vector<int>::iterator`
* Stawiając referencję `&` przy `auto`, wszelkie modifikatory (`const`, `volatile`) zostają zachowane (jeśli nie, są pomijane)

```cpp
const vector<int> values;
auto v1 = values;           // v1 : vector<int>
auto& v2 = values;          // v2 : const vector<int>&
```

* Tablice:

```cpp
Gadget items[10];
auto g1 = items             // g1 : Gadget*
auto& g2 = items;           // g2 : Gadget(&)[10] - referencja do tamtej tablicy
```

* Przed C++11 `auto` oznaczało zmienną automatyczną tworzoną na stosie (w przeciwieństwie do `new` tworzonej na stercie)

#### Wskaznik nullptr

* Nie wskazuje na żadną wartość
* Tworząc wskaźnik bez przypisania mu wartości na jaką ma wskazywać, dobrą praktyką jest pisanie wąsatych nawiasów - oznacza to ustawienie wskaźnika na `nullptr`
* Ma specjalny typ `std::nullptr_t`

```cpp
int* p {};  // p = nullptr
```

```cpp
void foo(int);

foo(0);         // zawoła foo(int)
foo(NULL);      // zalezy od kompilatora
foo(nullptr);   // compile-time error
```

#### Szablony wariadyczne

**Niedokończone**
Szablon ze zmienną ilością argumentów

#### Aliasy using

* Dobrą praktyką jest używanie w kodzie nazw domenowych, łatwiej zrozumieć kod

```cpp
using Pair = std::pair<float, char>;

typedef std::pair<float, char> Pair;    // odpowiednik z typedef
```

* Przewagą `using` nad `typedef` jest, że `using` można użyć z szablonami

```cpp
template <typename T>
using StrKeyMap = std::map<std::string, T>

StrKeyMap<int> myMap;   // std::map<std::string, int>
```

* Ciekawostka: `typedef` nie ma zdefiniowanej kolejności podawania argumentów dlatego 2 poniższe linijki są równoważne

```cpp
std::pair<float, char> typedef Pair;

typedef std::pair<float, char> Pair;
```


---

#### Literaly binarne

Od C++14 można stosować zapis binarny.

```cpp
int x = 0B001101;
```

#### Lambdy generyczne

Od C++14 parametr zadeklarowany w lambdzie jako `auto` zachowuje się jako parametr szablonowy (polimorficzne lambdy).

```cpp
auto identity = [](auto x) { return x; };

int three = identity(3);                // == 3
std::string foo = identity("foo");      // == "foo"
```

#### Wyrazenia przechwytujace lambda

C++14 umożliwia przechwycenie przez przeniesienie (`std::move`) jak i deklarowanie dowolnych obiektów wewnątrz funkcji lambda, bez konieczności istnienia relatywnej zmiennej w zasięgu zewnętrznym.

* W C++11 funkcja lambda mogła przechwycić zmienne zewnętrzne tylko przez wartość lub przez referencję!

```cpp
std::unique_ptr<int> ptr(new int(10));

// Lambda sama dedukuje typ z wyrażenia inicjalizującego
auto lambda = [value = std::move(ptr)]{
    return *value; };
```

#### Automatyczna dedukcja typu zwracanego z funkcji

Od C++14 można stosować `auto` jako typ zwracany z funkcji. Warunki:

* Wszystkie instrukcje `return` zwracają ten sam typ (o ile jest więcej niż jedna)
* W przypadku rekurencji, pierwsze napotkane `return` musi zwracać wartość nierekurencyjną
* Definicja funkcji musi znaleźć się przed pierwszym jej użyciem (deklaracja nie wystarczy)

```cpp
auto multiply(int x, int y) {
    return x * y;
}
```

```cpp
int fun1(int i);
auto fun2(int i);

int main() {
    std::cout << fun1(1) << std::endl; // Ok
    std::cout << fun2(1) << std::endl; // Błąd

    // Należy przesunąć definicji funkcji fun2() przed main()
    
    return 0;
}

int fun1(int i) { return i; }
auto fun2(int i) { return i; }
```

#### decltype(auto)

Od C++14 deklaracja `decltype(auto)` jako typu zwracanego z funkcji powoduje zastosowanie do dedukcji typu mechanizmu `decltype` (zachowującego referencje i modyfikatory const oraz volatile) zamiast mechanizmu auto.

```cpp
const int x = 0;
auto x1 = x;           // int
decltype(auto) x2 = x; // const int

int y = 0;
int& y1 = y;
auto y2 = y1;           // int
decltype(auto) y3 = y1; // int&

int&& z = 0;
auto z1 = std::move(z);             // int
decltype(auto) z2 = std::move(z);   // int&&
```

#### Rozluznienie ograniczen dla funkcji `constexpr`

C++11 wprowadził pojęcie funkcji deklarowanej jako `constexpr`, czyli  takiej, która może być wykonywana podczas kompilacji. Wartości zwracane takich funkcji mogą być użyte przez operacje wymagające stałych wyrażeń, takich jak argument typu całkowitego szablonu. Jednak funkcje `constexpr` w C++11 mogą zawierać tylko jedno wyrażenie, które jest zwracane oraz niewielką liczbę innych deklaracji.

```cpp
constexpr uint64_t fib(int x)
{
  if (x <= 2)
    return 1;
  return fib(x - 1) + fib(x - 2);
}

/*...*/
  constexpr uint64_t a = fib(35);
  std::cout << a;
```

**Od C++14** funkcje `constexpr` mogą zawierać:
* Wszelkie deklaracje z wyjątkiem zmiennych statycznych lub `thread_local`
* Instrukcje warunkowe `if` i rozgałęzienia `switch`
* Dowolne instrukcje pętli, w tym `for` dla zakresów
* Wyrażenia zmieniające wartość obiektu, jeśli zakres istnienia tego  biektu rozpoczął się w funkcji wyrażenia stałego
* Wywołania dowolnych metod niebędących `constexpr` za wyjątkiem metod `static`

W C++14 niestatyczne metody zadelkarowane jako `constexpr` są również domyślnie `const`. W C++14 nie są domyślnie `const`.

#### Szablony zmiennych

C++14 wprowadza szablony dla zmiennych. Przykładowe użycie:

```cpp
template<class T>
constexpr T pi = T(3.1415926535897932385);
template<class T>
constexpr T e  = T(2.7182818284590452353);
```

Poprzednie wersje języka umożliwiały tworzenie szablonów tylko z funkcji, klas oraz aliasów typów.

#### Atrybut `[[deprecated]]`

C++14 pozwala na oznaczenie konstrukcji przestarzałej. Konstrukcja taka jest nadal legalna, ale kompilator wyświetli komunikat ostrzegawczy. Opcjonalny literał łańcuchowy z komentarzem.

```cpp
[[deprecated]]
void foo();

[[deprecated("Use different function instead")]]
void foo();
```

#### Wiazania strukturalne

Od C++17 możliwe jest przypisanie zmiennym ochodzących:
* z tablicy o stałej wielkości
* z `std::pair`
* z `std::tuple`
* ze struktur
    * Żaden element nie może być statyczny
    * Wszystkie muszą być zdefiniowane w tej samej klasie bazowej

```cpp
auto [zmienna_1, zmienna_2, zmienna3] = egzemplarz tablicy, pary, tupli, struktury;
```

* Liczba zmiennych musi odpowiadać liczbie zmiennych znajdujących się po prawej stronie
* Przed C++17 możliwe to było przy zastosowaniu `std::tie`, ale wszystkie zmienne muszą być wcześniej zadeklarowane
    * Pewnym ułatwieniem w `std::tie` jest zastosowanie `std::ignore` czyli niedeklarowanie zmiennej której nie będziemy potrzebować

```cpp
auto pair = std::make_pair<int, std::string>(1, "Matrix");

std::string name;
std::tie(std::ignore, name) = pair;

std::cout << name;
```

#### Mozliwosc inicjalizacji zmiennych w konstrukcjach if oraz switch

W C++17 wprowadzono dodatkową składnię dla instrukcji `if` oraz `switch` umożliwiającą zgrupowanie instrukcji inicjującej oraz sprawdzającej warunek. Ogarniczamy w ten sposób zasięg zmiennych

```cpp
if (init; condition)
{}

switch(init; condition)
{}
```

**Przykłady**

```cpp
if (Status status = g.status(); status == Status::bad) {
    std::cerr << "Gadget is broken(status=" << static_cast<int>(status) << std::endl;
}

if (std::lock_guard<std::mutex> lk{mtx}; !q.empty()) {
    std::cout << q.front() << std::endl;
}

switch (Gadget g{2}; auto s = g.status()) {
case Status::on:
    cout << "Gadget is on" << endl;
    break;
case Status::off:
    cout << "Gadget is off" << endl;
    break;
case Status::bad:
    cout << "Gadget is broken" << endl;
    break;
}
```

#### Statyczne pola wbudowane

Od C++17, statyczne pole w klasie z nagłówkiem `inline` nie wymaga definicji poza klasą.

```cpp
class Entity {
    static int index = 1;
    inline static int newIndex = 1;
};

int Entity::index = 1;        // DOBRZE     
int Entity::newIndex = 1;     // ŹLE
```

#### Automatyczna dedukcja typu szablonu klasy

Od C++17 kompilator sam wydedukuje typ szablonu klasy w oparciu o typ danych który zostanie podany do konstruktora.

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
}
```

#### `constexpr` lambdy

Od C++17 możliwe są `constexpr` lambdy.

```cpp
auto identity = [](int n) constexpr { return n; };

static_assert(identity(123) == 123);
```

```cpp
constexpr auto sum = [](int a, int b){ return a + b; };

static_assert(sum(3, 7) == 10, "3 + 7 == 10");
```

#### Atrybuty `fallthrough`, `nodiscard`, `maybe_unused`

`[[fallthrough]]` - wskazuje na zamierzony brak instrukcji break po instrukcji case i kompilator nie powinien wysyłać ostrzeżeń.

```cpp
switch (n) {
  case 1: [[fallthrough]]
    // ...
  case 2:
    // ...
    break;
}
```
`[[nodiscard]]` - 

`[[maybe unused]] ` - 

---

#### std::make_unique

```cpp
auto uniqueEntity = std::make_unique<Entity>(); // działa od C++14

std::unique_ptr<Entity> entity(new Entity());
```

* Rekomendowane tworzenie wskaźnika poprzez `std::make_unique`
    * Brak użycia używamy operatora `new`
    * Brak powtórzeń typu w kodzie (`std::make_shared<T>{new T}`)
    * Bezpieczene w przypadku wystąpienia wyjątku

```cpp
foo(std::unique_ptr<T>{new T}, function_that_throws(), std::unique_ptr<T>{new T});

// kompilator nie gwarantuje kolejności wykonania, a możliwe że 
// nastąpi przeplot operacji. Zawołanie operatora new to:
// * Alokacja pamięci na stercie
// * Wywołanie konstruktora
// * Przypisanie adresu pamięci do wskaźnika
```

* Rekomendowane przekazywane do funkcji `std::unique_ptr` jako `const&`

```cpp
void foo(const std::unique_ptr<Entity>& p);
```

#### Literaly dla `std::complex`, `std::chrono` i `std::string`

```cpp
#include <chrono>
#include <complex>
#include <string>

using namespace std::string_literals;
using namespace std::complex_literals;
using namespace std::chrono_literals;

int main() {    
    auto st = "Book"s;  // std::string

    auto i = 1i;        // complex<double>

    auto h = 1h;        // chrono::hours
    auto m = 1min;      // chrono::minutes
    auto s = 1s;        // chrono::seconds

    std::chrono::duration_cast<std::chrono::minutes>(h).count(); // == 60
}
```

#### std::variant

Dostępna od C++17 alternatywa dla Unii. Cechy:
* Automatycznie wołany destruktor (po wyjściu poza zakres)
* W przypadku próby pobrania niewłaściwego typu rzucany jest wyjątek
* Tworzenie `std::variant`

```cpp
std::variant<int, std::string> v1;

v1 = 5;

```

* Dostęp do wartości albo przez index albo przez typ

```cpp
std::variant<int, std::string> v2;
v2 = 10;

auto a = std::get<0>(v);
auto b = std::get<int>(v);
```

* Umożliwia przechowywanie wielu wartości tego samego typu:
    * Inicjalizowanie wartością wymaga użycia `std::in_place_index`
    * Dostęp do wartości tylko przez index

```cpp
std::variant<int, int> v3;
std::variant<int, int> v1(std::in_place_index<1>, 12);

auto c = get<1>(v3);
auto d = get<int>(v3);  // ŹLE
```

---

#### Inteligentne wskazniki

* Obiekty klas wprowadzonych w standardzie C++11 udoskonalające działanie wskaźników (wymaga `#include <memory>`).
* Ułatwiają zarządzanie pamięcią - programista nie musi pamiętać o zwolnieniu pamięci (`delete`) - dzieje się to automatycznie. Nowsze wersje języka C++ zwalniają nawet z pisania `new`.
    * W bardziej skomplikowanym kodzie, z kilkoma instrukcjami warunkowymi, w każdej możliwej ścieżce musimy mieć kod, który poprawnie zwolni pamięć.

Używając tradycyjnych RAW pointerów przez przypadek:
* Usunąć wskaźnik nie zwalniając pamięci na którą on wskazuje (jeśli żaden inny wskaźnik nie wskazuje na ten obszar to nie mamy już dostępu do tych danych)
* Zwolnić ten sam obszar pamięci kilkakrotnie
* Zdereferencjonować wskaźniki null

To wszystko wysypie program.

##### `std::unique_ptr`

* Może istnieć tylko 1 obiekt wskazujący na dane miejsce w pamięci (posiada on prawo właności do przechowywanej wartości)
* Możliwe przenoszenie (`std::move`)
* Niemożliwe kopiowanie
    * Dozwolone jest tylko przesyłanie jego referencji do funkcji (jako argument)
* Brak narzutu pamięciowego i czasu wykonania
* Wskaźnik, który w momencie wyjścia poza zakres (lub po wywołaniu metody `reset`), sam się usuwa i zwalnia pamięć
* Korzysta się z nich tak samo jak z RAW pointerów. Różni się jedynie ich tworzenie
* Możliwy własny deleter
    * `std::unique_ptr` nie musi zarządzać pamięcią, może czymś innym (np. zamykać plik)
    * Własny deleter zwiększa rozmiar `std::unique_ptr` bo nie ma on bloku kontrolnego

```cpp
// TWORZENIE
auto entity = std::make_unique<Entity>(); // działa od C++14
std::unique_ptr<Entity> entity(new Entity()); // druga opcja, mniej polecana

// PRZENOSZENIE
std::unique_ptr<Entity> p1();
std::unique_ptr<Entity> p2 = std::move(p1); // p1 = nullptr

// WYSYŁANIE DO FUNKCJI
void process(std::unique_ptr<Entity> obj);

auto p = std::make_unique<Entity>();
process(p);                 // ŹLE!
proecss(std::move(p));      // DOBRZE

// WSTAWIANIE DO KOLEKCJI
auto p = std::make_unique<Entity>();
std::vector<std::unique_ptr<Entity>> v;

v.push_back(p);             // ŹLE!
v.push_back(std::move(p));  // DOBRZE
```

##### `std::shared_ptr`

```cpp
auto sharedEntity = std::make_shared<Entity>();
std::shared_ptr<Entity> entity(new Entity());
```

* Możliwe przenoszenie
* Możliwe kopiowanie
* Rekomendowane tworzenie wskaźnika poprzez `std::make_shared`
    * Brak użycia używamy operatora `new`
    * Brak powtórzeń typu w kodzie (`std::shared_ptr<T>{new T}`)
    * Lepsze ułożenie w pamięci: blog kontrolny i dane użytkownika są w 1 bloku w pamięci (**cache-friendly** oraz zajmuje mniej pamięci)
        * Alokując za pomocą `new` mamy 2 osobne bloki
    * Bezpieczene w przypadku wystąpienia wyjątku

```cpp
foo(std::shared_ptr<T>{new T}, function_that_throws(), std::shared_ptr<T>{new T});

// kompilator nie gwarantuje kolejności wykonania, a możliwe że 
// nastąpi przeplot operacji. Zawołanie operatora new to:
// * Alokacja pamięci na stercie
// * Wywołanie konstruktora
// * Przypisanie adresu pamięci do wskaźnika
```

* Wprowadza narzut pamięci i czasu wykonania (inkrementacja licznika musi być bezpieczna wielowątkowo, wiele wątków nie może jednocześnie pisać do tego samego miejsca w pamięci)
* Trzeba uważać na cykle: sytuacja jeśli mamy `std::shared::ptr` wskazujące na 2 struktury przechowujące `std::shared_ptr`, które wskazują na siebie na wzajem). Pamięć nigdy nie będzie zwolniona (wyciek pamięci).

```cpp
struct S {
    S() { std::cout << "Constructor\n"; }
    ~S() { std::cout << "Destructor\n"; }

    std::shared_ptr<S> p_;
};

int main() {
    std::shared_ptr<S> p1(new S);
    std::shared_ptr<S> p2(new S);

    p1->p_ = p2;
    p2->p_ = p1;
}
```

> `Constructor`<br>
> `Constructor`

* Blok na dane + blok kontrolny
* Bloku kontrolny
    * Licznik `std::shared_ptr` (`std::atomic<int>`)
    * Licznik `std::weak_ptr` (`std::atomic<int>`)
    * Deleter (domyślnie `detele`, może być własna funkcja, funktor lub lambda)
        * Chcąc skorzystać z własnego deletera nie można skorzystać z konstrukcji `std::make_shared`
        * W celu zapewnienia, że dane użytkownika i blok kontrolny trafią do jednego bloku w pamięci należy zastosować `allocate_shared` ale wymaga to zdefiniowania własnego allokatora.
* Destruktor
    * `shared-refcount`--
    * Usuwa obiekt gdy `shared-refcount == 0`
    * Usuwa blok kontrolny gdy `shared-refcount == 0` oraz `weak-refcount == 0`

* Po zniszczeniu ostatniej, dopiero wtedy następuje zwolnienie pamięci.
* Rekomendowane przekazywane do funkcji `std::shared_ptr` jako `const&`

```cpp
void foo(const std::shared_ptr<Entity>& p);
```

##### `std::weak_ptr`

* W celu stworzenia `std::weak_ptr` trzeba mu przekazać do konstruktora `std::shared_ptr`-a
* Stosuje się go wraz z `std::shared_ptr`, ale nie inkrementuje to licznika referencji

```cpp
std::shared_ptr<S> sp(new S);
std::weak_ptr<S> wp(sp);

std::cout << sp.use_count() << std::endl;   // Wyświetla 1
```

* Żeby dostać się do wartości na którą wskazuje `std::weak_ptr` należy zablokować zasób `lock()`. Jeśli to się uda `lock()` zwróci `std::shared_ptr`. Jeśli zasób na który wskazywał `std::weak_ptr` został już zwolniony, `lock()` zwróci nullptr.

```cpp
std::shared_ptr<S> p1(new S);
std::weak_ptr<S> p2(p1);

auto p = p2.lock();
if (p) {
    std::cout << p->i;
}
```
* Destruktor
    * `weak-refcount`--
    * Usuwa blok kontrolny gdy `shared-refcount == 0` oraz `weak-refcount == 0`
* Rozwiązanie problemu cyklów

```cpp
struct S {
    S() { std::cout << "Constructor\n"; }
    ~S() { std::cout << "Destructor\n"; }

    std::weak_ptr<S> p_;        // zamiast std::shared_ptr
};

int main() {
    std::shared_ptr<S> p1(new S);
    std::shared_ptr<S> p2(new S);

    p1->p_ = p2;
    p2->p_ = p1;
}
```

> `Constructor`<br>
> `Constructor`<br>
> `Desctuctor`<br>
> `Desctuctor`
