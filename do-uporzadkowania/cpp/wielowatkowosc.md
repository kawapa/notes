# Spis treści

## [Wielowątkowość: wątki](#wielow%C4%85tkowo%C5%9B%C4%87-w%C4%85tki-1)

1. [Kiedy stosować współbieżność?](#1-kiedy-stosowa%C4%87-wsp%C3%B3%C5%82bie%C5%BCno%C5%9B%C4%87)
2. [Kiedy nie stosować współbieżności?](#2-kiedy-nie-stosowa%C4%87-wsp%C3%B3%C5%82bie%C5%BCno%C5%9Bci)
3. [Wątki, podstawowe operacje na wątkach](#3-w%C4%85tki-podstawowe-operacje-na-w%C4%85tkach)
4. [Przekazywanie argumentów do funkcji w wątku](#4-przekazywanie-argument%C3%B3w-do-funkcji-w-w%C4%85tku)
5. [Wyjątki w wątkach](#5-wyj%C4%85tki-w-w%C4%85tkach)
6. [Usypianie wątków](#6-usypianie-w%C4%85tk%C3%B3w)

## [Współdzielenie danych](#wsp%C3%B3%C5%82dzielenie-danych-1)

7. [Niebezpieczeństwa podczas używania wielowątkowości](#7-niebezpiecze%C5%84stwa-podczas-u%C5%BCywania-wielow%C4%85tkowo%C5%9Bci)
8. [Thread sanitizer](#8-thread-sanitizer)
9. [Debugger](#9-debugger)
10. [Mutexy](#10-mutexy)
11. [Menadżery blokad](#11-menad%C5%BCery-blokad)

## [Zmienne atomowe](#zmienne-atomowe-1)

12. [Model pamięci](#12-model-pami%C4%99ci)
13. [Jak synchronizować?](#13-jak-synchronizowa%C4%87)
14. [`std::atomic`](#14-stdatomic)
15. [`std::atomic` i `std::memory_order`](#15-stdatomic-i-stdmemory_order)

## [Zmienne warunku](#zmienne-warunku-1)

16. [Aktywne czekanie (spinlock)](#16-aktywne-czekanie-spinlock)
17. [`std::condition_variable`](#17-stdcondition_variable)
18. [`std::condition_variable_any`](#18-stdcondition_variable_any)
19. [Zagrożenia dla `std::condition_variable` / `std::condition_variable_any`](#19-zagro%C5%BCenia-dla-stdcondition_variable--stdcondition_variable_any)

## [Komunikacja jednokierunkowa (future, promise)](#komunikacja-jednokierunkowa-stdfuture-i-stdpromise)

20. [Opis](#20-opis)
21. [Funkcje `std::future`](#21-funkcje-stdfuture)
22. [Obsługa wyjątków w `std::promise`](#22-obs%C5%82uga-wyj%C4%85tk%C3%B3w-w-stdpromise)
23. [`std::shared_future`](#23-stdshared_future)

## [Zadania asynchroniczne (`std::assync`)](#zadania-asynchroniczne-stdassync-1)

24. [Opis](#24-opis)
25. [Polityki uruchamiania](#25-polityki-uruchamiania)
26. [Problem domyślnej polityki](#26-problem-domyslnej-polityki)
27. [`std::packaged_task`](#27-stdpackaged_task)

## Wielowątkowość: wątki

### 1. Kiedy stosować współbieżność?

* Podział zagadnień
* Zwiększenie wydajności
  * Zrównoleglenie zadań (podział zadania na części)
  * Zrównoleglenie danych (wykonywanie tych samych zadań na różnych fragmentach danych)

### 2. Kiedy nie stosować współbieżności?

* Gdy zbyt wiele wątków może obniżyć wydajność zamiast ją zwiększyć (koszt uruchomienia wątku, koszt zasobów - każdy wątek zajmuje pamięć)
* Gdy wzrost wydajności nie jest proporcjonalny do włożonego wysiłku i złożoności kodu (koszt utrzymania kodu)

### 3. Wątki, podstawowe operacje na wątkach

Instrukcja | Opis
------------ | -------------
`std::thread name(function)` | <ul><li>Żeby pomyślnie utworzyć wątek **należy** przekazać mu: funkcję, funktor, obiekt funkcyjny (jest on kopiowany do wątku), lambdę</li><li> Jeśli wątek nie dostanie żadnej funkcji `std::thread::joinable()` zwróci `false` </li><li> Jeśli nie wywołamy `join()` lub `detach()` otrzymamy `std::terminate` </li><li> Jeśli podczas wykonywania programu wyskoczy wyjątek, nie będzie możliwe złączenie wątku dlatego należy skorzystać z RAII (*Resource Acquisition Is Initialization*) - patrz "[Przykład 2](#przyk%C5%82ad-2)"</li><li> Kopiowanie wątku jest zabronione, możliwe jest natomiast przenoszenie (`std::move`) oraz zwracanie kopii wątku z funkcji (kompilator zoptymalizuje kod i "wyrzuci" zbędne kopiowanie (RVO - *Return Value Optimisation*) </li></ul>
`std::thread::joinable()` | <ul><li> Zwraca `true` jeśli można zrobić `join()` lub `detach()`</li></ul>
`std::thread::join()` | <ul><li>Jeśli chcemy poczekać aż wątek zakończy wszystkie swoje obliczenia i chcemy go zsynchronizować w jakimś miejscu</li><li>Program nie pójdzie dalej póki wątek nie zakończy pracy</li></ul>
`std::thread::detach()` | <ul><li> Nie czekamy na wątek tylko idziemy dalej </li><li> Tracimy nad nim kontrolę, ale on dalej pracuje i zwróci wynik jak skończy (o ile `main()` nie zakończy się szybciej) </li><li> Każdy wątek posiada odrębny stos (adres powrotu z funkcji oraz zmienne lokalne). Wywołując `detach()` wszystko się zwija.</li></ul>
`std::thread::hardware_concurrency()` | <ul><li> Zwraca ile procesor ma wątków sprzętowych które może obsługiwać jednocześnie</li></ul>
`std::this_thread::get_id()` | <ul><li> Zwraca id wątku który wykona tą operację </li><li> `std::thread::id this_id = std::this_thread::get_id();`</li></ul>

#### Przykład 1

```cpp
#include <thread>
#include <iostream>

struct Bar1 {
    void operator()() { std::cout << "Hello World!"; }
};

struct Bar2 {
    void foo() { std::cout << "Hello World!"; }
};

void foo() {
    std::cout << "Hello World!";
}

int main() {

    std::thread t1([]() { std:: cout << "Hello World!"; });

    std::thread t2(foo);
    // przekazanie funkcji

    std::thread t3(&foo);
    // przekazanie referencji do funkcji

    Bar1 bar1;
    std::thread t4(bar1);
    // bo przeciążony operator wywołania

    std::thread t5(*foo);
    // przekazanie wskaźnika na funkcję

    Bar2 bar2;
    std::thread t6(&Bar2::foo, &bar2);

    t1.join();
    t2.join();
    t3.join();
    t4.join();
    t5.join();
    t6.join();
}
```

#### Przykład 2

```cpp
/*
ThreadGuard otrzymuje referencje do wątku i w momencie wyjścia poza zakres sprawdza
czy `joinable()` zwraca `true`. Jeśli tak to go łączy. Można też użyć move semantics
i przenieść wątek.
*/
class ThreadGuard
{
    std::thread &t;

public:

    explicit ThreadGuard(std::thread& t_) : t(t_) { }
    ~ThreadGuard() {
        if (t.joinable())
            t.join();
    }
};

void fun() { }

int main()
{
    std::thread t(fun);
    ThreadGuard g(t);
    // lub ThreadGuard(std::thread(fun));
}
```

### 4. Przekazywanie argumentów do funkcji w wątku

#### Przekazywanie przez wartość

```cpp
Struct SomeStruct { };

void bar(int x, std::string str, SomeStruct obj) { }

int main() {

    std::thread t(bar, 10, "String", SomeStruct{});
    t.join();
}
```

#### Przekazywanie przez referencję

Jeśli funkcja przyjmuje wartość, a dostanie referencję to to co tak naprawdę się wydarzy to:

* Stworzy kopię zmiennej
* ...i przekażemy referencję do kopii (inne wątki nie będą widziały zmian)

Rozwiązaniem problemu jest zastosowanie *wrapper'a* `std::ref` (lub `std::cref()` dla referencji stałych).

```cpp
void bar(int& x, int* y) {
    std::cout << "Inside fun: = " << x << " | y = " << *y << std::endl;
    x = 20;
    *y = 30;
}

int main() {
    int x = 10;
    int y = 10;
    std::thread t(bar, std::ref(x), &y);
    t.join();
    std::cout << "Outside fun: x = " << x << " | y = " << y << std::endl;

    return 0;
}
```

**Uwaga! Należy zawsze upewnić się, że długość życia zmiennej do której się odwołujemy zawsze była dłuższa niż długość życia wątku, który na niej operuje.** Przykład:

```cpp
#include <thread>

void do_sth([[maybe_unused]] int i) { /* ... */ }

struct A {
    int& ref_;
    A(int& a) : ref_(a) {}
    void operator()() {
        do_sth(ref_); // potential access to
                      // a dangling reference
    }
};

std::thread create_thread() {
    int local = 0;
    A worker(local);
    std::thread t(worker);
    return t;
} // local is destroyed, reference in worker is dangling

int main() {
    auto t = create_thread();  // Undefined Behavior
    auto t2 = create_thread(); // Undefined Behavior
    t.join();
    t2.join();
    return 0;
}
```

#### Przekazywanie metody klasy

* Metoda klasy opalana w wątku jako kolejny (ukryty) parametr przyjmuje wkaźnik do obiektu na którym ma zostać wywołana
    * `std::thread t(&Car::setData, &toyota, 2015, "Corolla")` mimo że, deklaracja funkcji `setData()` to `void setData(int year, const string & model)`
    * `Corolla` nie wymaga `std::ref()` bo to zmienna tymczasowa (można podpiąć pod `const &`) 
    * Metoda (funkcja, lambda lub funktor) są kopiowane do pamięci wątku
    * Parametry są **kopiowane** lub **przenoszone**

##### Przykład 1 (pułapka w kodzie)

```cpp
void f(int i, std::string const& s);
void oops(int arg)
{
    char buffer[1024];
    sprintf(buffer, "%i", arg);
    std::thread t(f, 3, buffer);
    t.detach();
}
```

Niejawna konwersja i potem `detach()`. Najpewniej konwersja z `char[]` na `std::string` nie zdąży się zakończyć.

##### Rozwiązanie:

```cpp
void f(int i, std::string const& s);
void oops(int arg)
{
    char buffer[1024];
    sprintf(buffer, "%i", arg);
    std::thread t(f, 3, std::string(buffer));
    t.detach();
}
```

### 5. Wyjątki w wątkach

* Nie można standardowo złapać wyjątków w innym wątku niż tym, który rzucił wątek
* Aby przechwycić wyjątek rzucony z innego wątku należy użyć wskaźnika na wyjątek `std::exception_ptr`
    * Wątek rzucający wyjątek przypisuje do niego `std::current_exception()`
    * Wątek, który chce złapać wyjątek sprawdza czy `std::current_expection() != 0)`. Jeśli tak, bieżący wątek rzuca dany wyjątek ponownie poprzez `std::rethrow_exception()`
* Warto używać w wyjątkach funkcji funkcji `noexcept` - wtedy wyjątki nie będą rzucane

```cpp
#include <iostream>
#include <thread>
#include <exception>
#include <stdexcept>

int main()
{
    std::exception_ptr thread_exception = nullptr;
    std::thread t([](std::exception_ptr & te) {
        try {
            throw std::runtime_error("WTF");
        } catch (...) {
            te = std::current_exception();
        }
    }, std::ref(thread_exception));
    t.join();

    if (thread_exception) {
        try {
            std::rethrow_exception(thread_exception);
        } catch (const std::exception & ex) {
            std::cout << "Thread exited with an exception: " << ex.what() << "\n";
        }
    }
    return 0;
}
```

### 6. Usypianie wątków

Instrukcja | Opis
------------ | -------------
`#include <chrono>` | <ul><li>Biblioteka ułatwiające operacje na datach i czasie</li></ul>
`using namespace std::chrono_literals` | <ul><li> Pozwala na stosowanie skrótów np. 2s lub 2000ms zamiast `std::chrono::seconds(2)` lub `std::chrono::milliseconds(2000)`</li></ul>
`std::this_thread::sleep_until()` | <ul><li> Usypia wątek do pewnego momentu w czasie</li></ul>
 `std::thread::sleep_for()` | <ul><li> Usypia wątek na jakiś okres czasu </li><li> Wartość podana w argumencie to minimalny gwarantowany czas</li></ul>

#### Przykład 1

```cpp
#include <chrono>
#include <iostream>
#include <thread>

int main()
{
    using namespace std::chrono_literals;

    std::cout << "Hello waiter\n" << std::flush;
    auto start = std::chrono::high_resolution_clock::now();

    std::this_thread::sleep_for(2s);

    auto end = std::chrono::high_resolution_clock::now();
    std::chrono::duration<double, std::milli> elapsed = end - start;

    std::cout << "waited " << elapsed.count() << " ms\n";
}
```

Wyjście:

```bash
>> Hello waiter
>> Waited 2000.12 ms
```

## Współdzielenie danych

### 7. Niebezpieczeństwa podczas używania wielowątkowości

Zjawisko | Opis
------------ | -------------
Deadlock | <ul><li> [Przykład](#przyk%C5%82ad-deadlock) </li><li> Sytuacja, w której conajmniej dwa różne wątki czekają na siebie i żadna nie może pójść dalej</li><li> Występuje losowo przy niektórych uruchomieniach programu </li><li> Zalecane użycie `std::scoped_lock` do rozwiązania problemu zakleszczenia</li><li> Operacja czekania na wątek nie blokuje procesora </li></ul>
Race conditions | <ul><li> [Przykład](#przyk%C5%82ad-race-conditions) </li><li> Sytuacja, w której dwa lub więcej procesów wykonuje operację na zasobach dzielonych, a ostateczny wynik tej operacji jest zależny od momentu jej realizacji </li><li> Wyścig = undefined behaviour </li><li> Aby zapobiec warunkom wyścigu należy stworzyć mechanizm zabraniający więcej niż jednemu wątkowi dostępu do zasobów dzielonych w tym samym czasie </li><li> Do wykrywania tego zjawiska służy tzw. Thread Sanitizer (*TSan*, *Data race detector* wbudowany g++ oraz clang) </li><li> Debugger spowalnia wykonywanie operacji dlatego czasem może być ciężko wykryć coś takiego</li></ul>
Data races | <ul><li>Wyścig na danych, wartość zmiennej będzie zależeć od tego jak zostaną zakolejkowane wątki i może być różna przy różnych uruchomieniach programu</li></ul>
Zagłodzenie procesu/wątku | <ul><li> Sytuacja, w której przynajmniej jeden wątek nigdy nie dostanie wszystkich wymaganych zasobów</li></ul>
Livelock | <ul><li> Podobny do Deadlock przy czym stan dwóch procesów związanych z blokadą zmienia się w stosunku do drugiego procesu </li><li> Deadlock powoduje nieskończone oczekiwanie podczas gdy Livelock, jest szczególnym przypadkiem deadlocka, który powoduje marnowanie cykli procesora</li></ul>

#### Przykład *Deadlock*

```cpp
#include <thread>
#include <mutex>

using namespace std;

class X {

    mutable mutex mtx_;
    int value_ = 0;

public:
    explicit X(int v) : value(v) {}

    bool operator< (const X & other) const {
        lock_guard<mutex> ownGuard(mtx_);
        locK_guard<mutex> otherGuard(other.mtx_);
        // Rozwiazanie:
        // std::scoped_lock l(mtx_, other.mtx_);

        return value_ < other.value_;
    }
};

int main() {

    X x1(5);
    X x2(6);

    thread t1([&](){ x1 < x2; });
    thread t2([&](){ x2 < x1; });

    t1.join();
    t2.join();

    return 0;
}
```

* Słowo kluczowe `mutable` przy mutexie oznacza tyle, że nawet w przypadku metody `const` stan mutexu `mtx_` może zostać zmieniony (wywoływanie `lock()` i `unlock()` to w pewnym sensie jego modyfikacja)
* Słowo kluczowe `explicit` przy konstruktorze blokuje niejawne konwersje
    * Więcej informacji o `explicit`:
        * https://www.youtube.com/watch?v=Rr1NX1lH3oE

#### Przykład *Race conditions*

```cpp
void abc(int &a) { a = 2; }
void def(int &a) { a = 3; }

int main()
{
    int x = 1;
    std::thread t1(abc, std::ref(x));
    std::thread t2(def, std::ref(x));

    t1.join();
    t2.join();

    std::cout << x << std::endl;
}
```

### 8. Thread sanitizer

`>> g++ 1.cpp -lpthread -fsanitize=thread -O2 -g`

`>> ./a.out`

`-g` - informacja dla debuggera

Jeśli nie ma "niebezpieczeństw", *thread sanitizer* nie pokaże "raportu".

### 9. Debugger

`>> g++ 1.cpp -lpthread -g`

`>> gdb --tui ./a.out`

Komenda | Opis
------------ | -------------
`b 5` | Ustawia breakpoint w 5 linii
`watch x` | Obserwowanie zmian zmiennej x (debugger zatrzyma się gdy nastąpi jej modyfikacja)
`c` | Kontynuowanie debugowania
`info threads` | Informacje o wątkach
`thread 3` | Przełączenie na wątek 3
`n` | Następna instrukcja
`fin` | Wykonanie wszystkiego do końca bieżącej funkcji
`up` | Przejście do wyższej ramki stosu
`down` | Przejście do niższej ramki stosu
`del br` | Usunięcie wszystkich breakpointów
CTRL + L | Odświeżenie widoku

### 10. Mutexy

* [`std::mutex`]()
* [`std::timed_mutex`, `std::recursive_mutex`, `std::recursive_timed_mutex`]()

Mutex | Opis
------------ | -------------
`std::mutex` | <ul><li> Blokuje jednoczesny dostęp do tzw. sekcji krytycznej </li><li> Preferowane jest użycie `lock_guard` i zablokowanie całej funkcji, a nie pojedynczych linii </li><li> Czas trwania blokady musi być możliwie jak najkrótszy </li><li> Nie blokujemy instrukcji `sleep_for()` </li><li> Zamiast wypisywać `std::cout << "Hello" << " World";` warto skleić stringa wcześniej za pomocą `std::stringstream`</li><li> Preferowane wykorzystanie jako składnik w klasie </li><li> W przypadku wystąpienia wyjątku `std::mutex` nie zostanie odblokowany i inne wątki nigdy nie skończą pracy. Stąd zalecane jest użycie ktoregoś z menadżerów blokad RAII </li><li> W przypadku stałych (`const`) nie ma potrzby stosowania blokad

#### Najważniejsze funkcje `std::mutex`

Funkcja | Opis
------------ | -------------
`void lock()` | <ul><li> Zablokowanie mutexu </li><li> Jeśli mutex jest zablokowany przez inny wątek to oczekujemy na odblokowanie</li></ul>
`void unlock()` | <ul><li> Odblokowanie mutexu</li></ul>
`bool try_lock()` | <ul><li> Próba zablokowania mutexu (zwraca `true` jeśli udało się zablokować i blokuje, `false` jeśli mutex zajęty i nie udało się go zablokować).</li><li>Jest operacją nieblokującą i w przypadku nieudanej próby zajęcia mutexu możemy wykonać inne czynności</li></ul>

#### Przykład

```cpp
#include <vector>
#include <thread>
#include <chrono>
#include <iostream>
#include <mutex>

void do_work(int id)
{
    this_thread::sleep_for(100ms);
    m.lock();
    cout << ss.rdbuf();
    m.unlock();
}

int main()
{
    std::mutex m;
    std::vector<std::thread> threads;

    for (int i = 0; i < 20; i++)
        threads.emplace_back(thread(do_work, i));

    for (auto && t : threads)
        t.join();

    return 0;
}
```

Pozostałe mutexy | Opis
------------ | -------------
`std::timed_mutex` | <ul><li> Dwie dodatkowe operacje oprócz standardowych z `std::mutex`: <ul><li> `bool try_lock_until(timeout_time)` - spróbuj zablokować do jakiegoś punktu w czasie (zwraca true jak się udało, false jeśli nie) </li><li>`bool try_lock_for(timeout_duration)` - spróbuj zablokować na jakiś czas (zwraca true jak się udało, false jeśli nie)</li></ul></li></ul>
`std::recursive_mutex` | <ul><li> Ten sam wątek może wielokrotnie pozyskać mutex poprzez wywołanie metody `lock()` lub `try_lock` </li><li> Aby zwolnić mutex wątek musi taką samą ilość razy go odblokować</li><li>Zazwyczaj stosowany w algorytmach rekurencyjnych</li></ul>
`std::recursive_timed_mutex` | <ul><li> Połączenie `std::timed_mutex` oraz `std::recursive_mutex`
`std::shared_mutex` | <ul><li> Możliwość pozyskiwania blokad współdzielonych przy pomocy <ul><li> `void lock_shared()` </li><li> `bool try_lock_shared()` </li><li> `bool try_lock_shared_for(real_time)` </li><li> `bool try_lock_shared_until(abs_time)` </li><li> `void unlock_shared()`</li></ul>

### 11. Menadżery blokad

Pozostałe mutexy | Opis
------------ | -------------
`std::lock_guard<mutex>` | <ul><li> [Przykład](#przyk%C5%82ad-stdlock_guardmutex) </li><li> Najprostszy, główny wybór <ul><li> Nie ma funkcji odblokowania, blokuje tak długo jak żyje </li><li> Blokuje w konstruktorze </li><li> Odblokowuje w destruktorze</li><li> Przyjmuje dodatkowy patarametr - [czytaj](#dodatkowe-parametry-dla-std:lock_guard-stdunique_lock-oraz-stdshared_lock) </li></ul>
`std::unique_lock<mutex>` | <ul><li> Opóźnione blokowanie </li><li> Próby zablokowania ograniczone czasowo </li><li> Blokowanie rekursywne </li><li> Podejmowanie nieblokujących prób pozyskania blokady - `try_lock()` </li><li> Obsługa `std::timed_mutex>` </li><li> Korzystanie ze zmiennych warunkowych (condition variable) </li><li> Niekopiowalny </li><li> Przenoszalny (`std::move`)</li><li> Przyjmuje dodatkowy patarametr - [czytaj](#dodatkowe-parametry-dla-std:lock_guard-stdunique_lock-oraz-stdshared_lock) </li></ul>
`std::scoped_lock` | <ul><li> [Przykład](#przyk%C5%82ad-stdscoped_lock) </li><li> **Przyjmuje różne mutexy** </li><li> Blokuje kilka mutexów </li><li> Zapobiega zakleszczeniom (deadlock) </li><li> Konstruktor blokuje wszystkie mutexy w bezpiecznej kolejności unikając blokad </li><li> Destruktor odblokowuje je w kolejności odwrotnej </li><li> Niekopiowalny</li></ul>
`std::shared_lock<shared_mutex>` | <ul><li> [Przykład](#przyk%C5%82ad-stdshared_lockstdshared_mutex) </li><li> Menadżer współdzielonych blokad do odczytu </li><li> Kilka wątków może współdzielić blokadę </li><li> Wątki zapisujące muszą pozyskać wyłączną blokadę (`std::lock_guard` lub `std::unique_lock`) </li><li> Te same własności co `std::unique_lock`</li><li> Przyjmuje dodatkowy patarametr - [czytaj](#dodatkowe-parametry-dla-std:lock_guard-stdunique_lock-oraz-stdshared_lock) </li></ul>

#### Przykład `std::lock_guard<mutex>`

```cpp
#include <vector>
#include <thread>
#include <chrono>
#include <iostream>
#include <mutex>
#include <sstream>

void do_work(int id, mutex & m)
{
    this_thread::sleep_for(100ms);

    stringstream ss;
    ss << "Thread [" << id << "]: " << "Job done!" << std::endl;

    lock_guard<mutex> lock(m);

    cout << ss.rdbuf();
    // Tak naprawdę nie trzeba tutaj mutexa bo to pojedyncza operacja
    // Nie da się jej "zakłócić"
}

int main()
{
    std::mutex m;
    std::vector<std::thread> threads;

    for (int i = 0; i < 20; i++)
        threads.emplace_back(thread(do_work, i, std::ref(m)));

    for (auto && t : threads)
        t.join();

    return 0;
}
```

#### Przykład `std::scoped_lock`

```cpp
#include <iostream>
#include <mutex>
#include <thread>

class X {
    mutable std::mutex mtx_;
    int value_ = 0;

public:
    explicit X(int v) : value_(v) {}

    bool operator<(const X & other) const {
        std::scoped_lock l(mtx_, other.mtx_);
        return value_ < other.value_;
    }
};

int main() {

    X x1(5);
    X x2(6);

    std::thread t1([&] {
        if (x1 < x2)
            std::cout << "x1 is less" << std::endl;
    });

    std::thread t2([&] {
        if (x2 < x1)
            std::cout << "x2 is less" << std::endl;
    });

    t1.join();
    t2.join();

    return 0;
}
```

#### Przykład `std::shared_lock<std::shared_mutex>`

```cpp
#include <deque>
#include <shared_mutex>

std::deque<int> ids;
std::shared_mutex mtxIds;

int getIdsIndex() { /* ... */ }
int getIdsIndex() { /* ... */ }
int getIdsIndex() { /* ... */ }

void reader() {
    int index = getIdsIndex();
    std::shared_lock<std::shared_mutex> lock(mtxIds);
    int value = ids[index];
    lock.unlock();
    process(value);
}

void writer() {
    int index = getIdsIndex();
    std::lock_guard<std::shared_mutex> lock(mtxIds);
    // lub std::unique_lock...
    ids[index] = newValue();
}
```

#### Dodatkowe parametry dla `std::lock_guard`, `std::unique_lock` oraz `std::shared_lock`


Wyżej wymienione blokady opcjonalnie przyjmują dodatkowy parametr w postaci `std::defer_lock` lub `std::adopt_lock`.

* `std::defer_lock`
    * Nie blokuje w momencie utworzenia tylko oczekuje na operację `std::lock()`
    * Stanowi to alternatywę dla `std::scope_lock`, który pojawił się dopiero w C++17

```cpp
bool operator<(const X & other) const
{
    std::unique_lock<std::mutex> l1(mtx_, defer_lock);
    std::unique_lock<std::mutex> l2(other.mtx_, defer_lock);
    std::lock(l1, l2);

    return value_ < other.value_;
}
```

* `std::adopt_lock`
    * Informacja dla konstruktora, że otrzyma on już zablokowane mutexy (wcześniej pojawia się std::lock()`
    * Działanie przeciwne do `std::defer_lock`

```cpp
bool operator<(const X & other) const
{
    std::lock(mtx_, other.mtx_);
    std::lock_guard<std::mutex> l1(mtx_, adopt_lock);
    std::lock_guard<std::mutex> l2(other.mtx_, adopt_lock);

    return value_ < other.value_;
}
```

**Funkcja `std::lock()` gwarantuje zablokowanie wszystkich mutexów bez zakleszczenia niezależnie od kolejności ich pozyskania.**

## Zmienne atomowe

### 12. Model pamięci

* Najmniejsza jednostka – 1 bajt
* Każdy bajt ma unikalny adres w pamięci
* Synchronizacja **nie jest potrzebna** jeśli zapisujemy coś wielowątkowo **do różnych obszarów pamięci**

```cpp
std::vector<int> v(10, 0);

for (int = 0; i < 10; i++)
    std::thread t([&]{ v[i] = i; });
```

* Synchronizacja **jest potrzebna** jeśli zapisujemy coś wielowątkowo **do tych samych obszarów pamięci**

```cpp
std::vector<int> v;

for (int = 0; i < 10; i++)
    std::thread t([&]{ v.emplace_back(i); });
```

* Synchronizacja jest potrzebna jeśli conajmniej jeden wątek zapisuje, a inne odczytują ten sam obszar pamięci
* Brak synchronizacji jeśli jest wymagana to *wyścig/undefined behaviour*
* Użycie `const` nie wymaga synchronizacji
* Link do pełnego artykułu - [czytaj](https://en.cppreference.com/w/cpp/language/memory_model)

### 13. Jak synchronizować?

```cpp
int a = 0;
std::mutex m;

std::thread t1([&] {
std::lock_guard<mutex> lg(m);
a = 1;
});

std::thread t2([&] {
std::lock_guard<mute> lg(m);
a = 2;
});
```

Lepsze rozwiązanie, które chroni przed wyścigiem (data-race) to zastosowanie zmiennej atomowej (`std::atomic`). W ten sposób zapewniamy odpowiednie porządkowanie operacji dostępu do pamięci.

```cpp
std::atomic<int> a = 0;
std::thread t1([&]{ a = 1; });
std::thread t2([&]{ a = 2; });
```

### 14. `std::atomic`

`std::atomic` | Opis
------------ | -------------
Cechy | <ul><li> Umożliwiają jednoczesny zapis i odczyt <ul><li> Nie ma potrzeby dodatkowego blokowania </li></ul></li><li> Pozwalają na prostą arytmetykę i operacje bitowe (**te których nie ma w standardzie języka wymagają blokowania mutexem**) </li><li> Wykorzystuje się tylko z typami prostymi (liczby i wskaźniki) </li></ul>
Najważniejsze operacje | <ul><li> `store()` - zapisuje wartość zmiennej atomowej (można podać dodatkowo `std::memory_order`) </li><li> `operator=()` - zapisuje wartość w zmiennej atomowej </li><li> `load()` - odczytuje wartość ze zmiennej atomowej (można podać dodatkowo `std::memory_order`) </li><li> `operator T()` - odczytuje wartość ze zmiennej atomowej</li></ul>

```cpp
// use store() /  load()
#include <atomic>
#include <iostream>
#include <mutex>
#include <thread>
#include <vector>

std::vector<int> generateContainer()
{
    std::vector<int> input = {2, 4, 6, 8, 10, 1, 3, 5, 7, 9};
    std::vector<int> output;
    std::vector<std::thread> threads;
    std::mutex m;

    // należy jeszcze wyciągnąć lambdę przed pętlę...
    for (auto i = 0u; i < input.size(); i++) {
        threads.emplace_back([&, i]{
            std::lock_guard<std::mutex> l(m);
            // wstawianie wątków do wektora jest bezpieczne (bo jest sekwencyjne)
            // należy zabezpieczyć wstawianie liczb do wektora
            // bo może być problem z iteratorami
            output.push_back(input[i]);
        });
    }

    // "i" przekazujemy przez kopię
    // 1 powód: jeśli "i" przekażemy przez referencję to wszystkie wątki mogą mieć
    // taką samą wartość "i", np. 5
    // 2 powód: część wstawiania może się odbyć dopiero przed join(), a wtedy
    // zmienna "i" już nie istnieje (dangling reference)

    for (auto && t : threads)
        t.join();

    return output;
}

std::vector<int> generateOtherContainer()
{
    int start {5};
    // nie trzeba std::atomic bo mamy mutex na if oraz else
    std::atomic<bool> add {true};
    std::vector<int> output;
    std::vector<std::thread> threads;
    std::mutex m;

    for (int i = 0; i < 10; i++) {
        threads.emplace_back([&, i]{
            if (add)
            {
                std::lock_guard<std::mutex> l(m);
                output.push_back(start+=i);
            }
            else
            {
                std::lock_guard<std::mutex> l(m);
                output.push_back(start-=i);
            }
            add = !add;
        });
    }

    for (auto && t : threads)
        t.join();

    return output;
}

void powerContainer(std::vector<int>& input)
{
    std::vector<std::thread> threads;

    for (auto i = 0u; i < input.size(); i++)
        threads.emplace_back([&, i]{ input[i]*=input[i]; });
    // nie ma potrzeby stosowania mutexu
    // za każdym razem zapisujemy w inny obszar pamięci

    for (auto && t : threads)
        t.join();
}

void printContainer(const std::vector<int>& c)
{
    for (const auto & value : c)
        std::cout << value << " ";
    std::cout << std::endl;
}

int main() {

    auto container1 = generateContainer();
    printContainer(container1);
    powerContainer(    // należy jeszcze wyciągnąć lambdę przed pętlę...container1);
    printContainer(container1);

    auto container2 = generateOtherContainer();
    printContainer(container2);
    powerContainer(container2);
    printContainer(container2);

    return 0;
}
```

### 15. `std::atomic` i `std::memory_order`

`std::memory_order` pozwala na dodatkową otymalizację i określa on typ synchronizacji. Więcej informacji - [czytaj](https://en.cppreference.com/w/cpp/atomic/memory_order).

Typ `std::memory_order` | Opis
------------ | -------------
`memory_order_relaxed` | <ul><li> Nie ma żadnej synchronizacji (tak jakby nie było `std::atomic`)</li></ul>
`memory_order_consume` | <ul><li> Do odczytu </li><li> Zagwarantowane, że kompilator nie zmieni kolejności operacji przed `load()` i `store()` </li></ul>
`memory_order_acquire` | <ul><li> Do odczytu </li><li> Zagwarantowane, że kompilator nie zmieni kolejności operacji przed `load()` i `store()` </li></ul>
`memory_order_release` | <ul><li> Do zapisu </li><li> Zagwarantowane, że kompilator nie zmieni kolejności operacji **po** `load()` i `store()` </li></ul>
`memory_order_acq_rel` | <ul><li> Do zapisu </li><li> Zagwarantowane, że kompilator nie zmieni kolejności operacji **po** `load()` i `store()` </li></ul> 
`memory_order_seq_cst` | <ul><li> **Domyślny wybór (jeśli nie wybrany inny)** </li><li> Blokowany jednoczesny odczyt i zapis </li></ul>

## Zmienne warunku

### 16. Aktywne czekanie (spinlock)

* Aktywne czekanie (busy waiting) to stan, w którym wątek ciągle sprawdza, czy został spełniony pewien warunek
* Inna nazwa tego problemu to wirująca blokada (spinlock)
* Problem rozwiązuje **zmienna warunku** (condition variable)

```cpp
void saveToFile(StringQueue & sq) {

    ofstream file("/tmp/sth.txt");

    while (file) {
        while (sq.empty()) { /* nop */ }
        
        file << sq.pop() << endl;
    }
}
```

### 17. `std::condition_variable`

`std::condition_variable` | Opis
------------ | -------------
Cechy | <ul><li> Działa tylko z `std::unique_lock` </li><li> Niekopiowalny </li><li> Wymaga `#include <condition_variable>` </li></ul>
Najważniejsze operacje | <ul><li> `wait()` – oczekuje na zmianę / blokuje obecny wątek dopóki nie zostanie on wybudzony </li><ul><li> Wątek który zablokował mutex, trafił na `wait()` ale nie otrzymał ani `notify_one()`, ani `notify_all()` **zwalnia mutex** i jest usypiany. Ponadto, mimo że nie było żadnego `notify()` może się on spontanicznie **wybudzić i (spróbować) zablokować z powrotem mutex by sprawdzić czy był `notify()` oraz (opcjonalnie) czy warunek podany w predykacie został spełniony**</li><li> Wymaga przekazania w argumencie: <ul><li> `std::unique_lock` lub... </li><li> `std::unique_lock` oraz predykat </li></ul></li></ul></li><li> `notify_one()` – wybudza jeden z wątków oczekujących na zmianę <ul><li> Nie mamy kontroli nad tym, który z wątków zostanie powiadomiony  </li><li> Jeśli na zmiennej warunku czeka kilka wątków i każdy ma inny predykat, to jego użycie może spowodować zakleszczenie. Wybudzony może zostać wątek, dla którego warunek nie został spełniony i jeśli żaden inny wątek nie zawoła `nofity_one()` lub `notify_all()` to wszystkie będą czekać </li></ul></li><li> `notify_all()` – wybudza wszystkie wątki czekające na zmianę </li><ul><li> **Preferowanany wybór**  </li><li>  Wątki te mogą konkurować o zasoby </li></ul><li> `wait_for()` - przyjmuje okres czasu po którym naśtąpi wybudzenie </li><ul><li> Opcjonalnie zwraca powód wybudzenia (czy z powodu timeout'u lub nie) </li></ul><li> `wait_until()` - przyjmuje punkt w czasie, w którym nastąpi wybudzenie </li><ul><li> Opcjonalnie zwraca powód wybudzenia (czy z powodu timeout'u lub nie) </li></ul></ul>

* Jeśli wywołamy którąkolwiek z funkcji `notify()`, a żaden wątek nie czeka (`wait()`) to "stracimy" takiego notify'a.

### 18. `std::condition_variable_any`

`std::condition_variable_any` | Opis
------------ | -------------
Cechy | <ul><li> Działa z każdym rodzajem blokad </li><li> Te same właściwości co `std::condition_variable` </li></ul>

### 19. Zagrożenia dla `std::condition_variable` / `std::condition_variable_any`

Zagrożenie | Opis
------------ | -------------
Fałszywe przebudzenie (spurious wakeup) | <ul><li> Wątek czekający na zmiennej warunku cyklicznie co pewien okres czasu wybudza się i sprawdza czy nie przyszła notyfikacja</li><li> W celu oczekiwania na zmiennej warunku wymagana co najmniej blokada `std::unique_lock`, gdyż podczas uśpienia wątek ją odblokowuje, a gdy wybudza się, aby sprawdzić notyfikację blokuje ją ponownie na chwilę, po czym znów ją odblokowuje i śpi dalej </li><li> Predykat dodany do funkcji `wait()` zapobiega fałszywym przebudzeniom, gdyż dodaje dodatkowy warunek, który musi być spełniony, aby wątek się wybudził </li></ul>
Utracona notyfikacja (lost wakeup) | <ul><li> Jeśli notyfikacja została wysłana zanim wątek oczekiwał na zmiennej, to jest ona utracona i nie wybudzi ona wątku </li><li> Problem można obejść, gdy pojawi się fałszywe przebudzenie </li><li> Jeśli wątek oczekiwał na zmiennej warunku z predykatem, to predykat musi być spełniony, inaczej fałszywe przebudzenie nie nastąpi </li></ul>

## Komunikacja jednokierunkowa (`std::future` i `std::promise`)

### 20. Opis

* `std::future` i `std::promise` razem tworzą jednokierunkowy kanał komunikacji między **dwoma wątkami**
* Wątek, który "ma coś zrobić" jako argument oprócz lambdy dostaje też `std::promise`
* Wątek, który ma odebrać wynik obliczeń wywołuje `future.get()`

```cpp
std::promise<int> promise; // typ wyniku
std::future<int> future = promise.get_future(); 
// tworzymy future przez wywołanie get_future() na std::promise
// w ten sposób tworzy się kanał komunikacji
// kolejne wywolanie promise.get_future() rzuci wyjątek

auto function = [](std::promise<int> promise
{
    promise.set_value(10;)
    // ustawiamy promise jakąś wartość
}

std::thread t(function, std::move(promise));
// do osobnego wątku przekazujemy lambdę z promise'm

std::cout << future.get() << std::endl;
// inny wątek woła get() by wyłuskać wartość
// kolejne wywołanie get() rzuci wyjątek

t.join();
```

### 21. Funkcje `std::future`

Funkcja | Opis
------------ | -------------
`get()` | <ul><li> **Służy do synchronizacji wątków** tj. jeden wątek nie pójdzie dalej tylko poczeka do momentu aż inny wątek czegoś nie zrobi<ul><li> `get()` czeka na inny wątek **i pobiera jego wynik** </li></ul></li><li> Można wywołać tylko raz </li><ul><li> Kolejne wywołanie rzuci wyjątek </li></ul></ul>
`valid()` | <ul><li> Zwraca `true` jeśli można go użyć (tzn. można na nim wywołać `get()` lub `wait()` (innymi słowy czy `std::future` nie został już "zużyty")</li></ul>
`wait()` | <ul><li> **Służy do synchronizacji wątków** tj. jeden wątek nie pójdzie dalej tylko poczeka do momentu aż inny wątek czegoś nie zrobi<ul><li> `wait()` nie pobiera wartości tylko czeka aż będzie dostępna </li></ul></li><li> Można wywołać wielokrotnie </li></ul>

### 22. Obsługa wyjątków w `std::promise`

* Na `std::promise` oprócz `set.value()` można wywołać `set.exception()`
* Wywołanie `get()` na "drugim" wątku rzuci wyjątek

```cpp
promise.set_exception(std::make_exception_ptr(e));

try {
// ...
} catch (...) {
promise.set_exception(std::current_exception());
}
```

### 23. `std::shared_future`

* Jeden wątek nadaje, ale wiele odbiera
    * Obiekt `std::promise` tworzymy tylko jeden
* Jedyna różnica w zastosowaniu to taka że zamiast `std::future<int> f = promise.get_future()` piszemy `std::shared_future<int> f = promise.get_future().share()`
    * Każdy wątek powinien utworzyć swój własny obiekt `shared_future`
* Kopiowalny
* Przenoszalny

## Zadania asynchroniczne (`std::assync`)

### 24. Opis

* `std::async` to wysokopoziomowe rozwiązanie, które automatycznie zarządza wywołaniami asynchronicznymi z podstawowymi mechanizmami synchronizacji
* Przykazując funkcję do `std::assync` zwróci on obiekt `std::future` za pomocą którego można się dostać do jego rezultatu (jak tylko będzie on dostępy)
    * Wywołując `get()` przed tym jak zostanie "obliczony" wynik, wątek poczeka na niego...
* Obsługa wyjątków przez `std::promise` i `std::future`
* Wymaga `#include <future>`
```cpp
std::future<int> f = std::assync(function)
std::cout << f.get() << std::endl;
```

### 25. Polityki uruchamiania

Przykład wykorzystania:

```cpp
auto f2 = async(launch::async, []{
        cout << "f2 started\n";
        this_thread::sleep_for(1s);
```

Polityka | Opis
------------ | -------------
`std::launch::async` | <ul><li> Wywołanie asynchroniczne w osobnym wątku</li></ul>
`std::launch::deferred` | <ul><li> Wywołanie funkcji (i obliczenie rezultatu) następuje dopiero po wywołaniu `get()` lub `wait()` </li><li> Wątek wywołujący `get()` lub `wait()` czeka na wątek zwracający wartość (punkt synchronizacji) </li></ul>
`std::launch::async::deferred` | <ul><li> Wywołanie asynchroniczne w osobnym wątku</li></ul>
Bez *launch policy* | <ul><li> **Undefined behaviour** (od C++14 - [czytaj](https://en.cppreference.com/w/cpp/thread/async)) </li></ul>

```cpp
#include <future>
#include <vector>
#include <iostream>
#include <chrono>
using namespace std;

int main()
{
    auto f1 = async([] {
        cout << "f1 started\n";
        this_thread::sleep_for(1s);
        return 42;
    });
    cout << "f1 spawned\n";
    
    auto f2 = async(launch::async, []{
        cout << "f2 started\n";
        this_thread::sleep_for(1s);
        return 2 * 42;
    });
    cout << "f2 spawned\n";
    
    auto f3 = async(launch::deferred, []{
        cout << "f3 started\n";
        this_thread::sleep_for(1s);
        return 3 * 42;
    });
    cout << "f3 spawned\n";

    cout << "Getting f1 result\n";
    auto v1 = f1.get();
    cout << "Got f1 result\n";
    
    cout << "Getting f2 result\n";
    auto v2 = f2.get();
    cout << "Got f2 result\n";
    
    cout << "Getting f3 result\n";
    auto v3 = f3.get();
    cout << "Got f3 result\n";
    
    vector<int> numbers = { v1, v2, v3 };
    for (const auto & item : numbers)
        cout << item << '\n';
    
    return 0;
}

```
Wyjście:
```bash
>> f1 spawned
>> f1 started
>> f2 spawned
>> f3 spawned
>> Getting f1 result
>> f2 started
>> Got f1 result
>> Getting f2 result
>> Got f2 result
>> Getting f3 result
>> f3 started
>> Got f3 result
>> 42
>> 84
>> 126
```

### 26. Problem domyślnej polityki

Nie można sprawdzić w jaki sposób future (`std::async`) został uruchomiony ale korzystając z `wait_for()` i tego że zwraca 1 z 3 statusów:
* `future_status::deferred`
* `future_status::ready`
* `future_status::timeout`

... można tak zdobyć wynik:

```cpp
#include <iostream>
#include <future>
using namespace std;

void f() {
  this_thread::sleep_for(1s);
}

int main() {
    auto fut = async(f);

    if (fut.wait_for(0s) == future_status::deferred) {
        cout << "Scheduled as deffered. Calling wait() to enforce execution\n";
        fut.wait();
    } else {
        while (fut.wait_for(100ms) != future_status::ready) {   
            cout << "Waiting...\n";
        }
        cout << "Finally...\n";
    }
}
```

### 27. `std::packaged_task`

* `std::packaged_task` nie wykonuje się od razu (w odróżnieniu od `std::async`), to użytkownik decyduje kiedy to ma się wykonać
* Przydatne jeśli chcemy do `std::async` przekazać parametry, których jeszcze nie mamy albo inny wątek/funkcja go oblicza
    *  Wtedy wątek tworzymy dopiero jak już mamy obliczone parametry

```cpp
auto globalLambda = [](int a, int b) {
    std::cout << "globalLambda:\n";    
    std::this_thread::sleep_for(std::chrono::seconds(1));
    return std::pow(a, b); 
};

// LOKALNE WYWOŁANIE

void localPackagedTask() {
    std::cout << "\nlocalPackagedTask:\n";
    std::packaged_task<int(int,int)> task(globalLambda);
    auto result = task.get_future();
    
    std::cout << "before task execution\n";
    task(2, 9);
    std::cout << "after task execution\n";
 
    std::cout << "getting result:\t" << result.get() << '\n';
}

// WYWOŁANIE W INNYM WĄTKU

void remotePackagedTask() {
    std::cout << "\nremotePackagedTask:\n";
    std::packaged_task<int(int,int)> task(globalLambda);
    auto result = task.get_future();
    
    std::cout << "before task execution\n";
    std::thread t(std::move(task), 2, 9);
    std::cout << "after task execution\n";

    t.detach(); // detach żeby było asynchronicznie (nie join())

    std::cout << "getting result:\t" << result.get() << '\n';
}

// TO SAMO TYLKO KORZYSTAJĄC Z ASYNC

void remoteAsync() {
    std::cout << "\nremoteAsync:\n";
    auto result = std::async(std::launch::async, globalLambda, 2, 9); 
    std::cout << "getting result:\t" << result.get() << '\n';
}

int main() {
    localPackagedTask();
    remotePackagedTask();
    remoteAsync();
}
```