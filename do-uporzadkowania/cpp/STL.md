# STL <!-- omit in toc --> 

- [Kontenery](#kontenery)
	- [1. Kontenery sekwencyjne](#1-kontenery-sekwencyjne)
		- [`std::array`](#stdarray)
		- [`std::vector`](#stdvector)
		- [`std::deque`](#stddeque)
		- [`std::list`](#stdlist)
		- [`std::forward_list`](#stdforward_list)
	- [2. Kontenery asocjacyjne](#2-kontenery-asocjacyjne)
		- [`std::set` / `std::multiset`](#stdset--stdmultiset)
		- [`std::map` / `std::multimap`](#stdmap--stdmultimap)
		- [`std::unordered_map` / `std::unordered_multimap` / `std::unordered_set` / `std::unordered_multiset`](#stdunordered_map--stdunordered_multimap--stdunordered_set--stdunordered_multiset)
	- [3. Adaptery](#3-adaptery)
		- [`std::stack`](#stdstack)
		- [`std::queue`](#stdqueue)
		- [`std::priority_queue`](#stdpriority_queue)
	- [4. Inne](#4-inne)
		- [`std::wstring`](#stdwstring)
		- [`std::vallaray`](#stdvallaray)
		- [`std::tuple`](#stdtuple)
		- [`std::bitset`](#stdbitset)
- [Algorytmy](#algorytmy)
	- [1. Algorytmy niemodyfikujące](#1-algorytmy-niemodyfikujące)
		- [`std::all_of`, `std::any_of`, `std::none_of`](#stdall_of-stdany_of-stdnone_of)
		- [`std::for_each`](#stdfor_each)
		- [`std::for_each_n`](#stdfor_each_n)
		- [`std::count` / `std::count_if`](#stdcount--stdcount_if)
		- [`std::mismatch`](#stdmismatch)
		- [`std::find`](#stdfind)
		- [`std::find_if`](#stdfind_if)
		- [`std::find_if_not`](#stdfind_if_not)
		- [`std::find_end`](#stdfind_end)
		- [`std::find_first_of`](#stdfind_first_of)
		- [`std::adjacent_find`](#stdadjacent_find)
		- [`std::search`](#stdsearch)
		- [`std::search_n`](#stdsearch_n)
	- [2. Algorytmy modyfikujące](#2-algorytmy-modyfikujące)
		- [`std::copy` / `std::copy_if`](#stdcopy--stdcopy_if)
		- [`std::copy_n`](#stdcopy_n)
		- [`std::copy_backward`](#stdcopy_backward)
		- [`std::move`](#stdmove)
		- [`std::move_backward`](#stdmove_backward)
		- [`std::fill`](#stdfill)
		- [`fill_n`](#fill_n)
		- [`std::transform` (!)](#stdtransform-)
		- [`std::generate` (!)](#stdgenerate-)
		- [`std::generate_n`](#stdgenerate_n)
		- [`std::remove, remove_if`](#stdremove-remove_if)
		- [`std::remove_copy`, `std::remove_copy_if`](#stdremove_copy-stdremove_copy_if)
		- [`std::replace`, `std::replace if`](#stdreplace-stdreplace-if)
		- [`std::replace_copy`, `std::replace_copy_if`](#stdreplace_copy-stdreplace_copy_if)
		- [`std::swap`](#stdswap)
		- [`std::swap_ranges`](#stdswap_ranges)
		- [`std::iter_swap`](#stditer_swap)
		- [`std::reverse`](#stdreverse)
		- [`std::reverse_copy`](#stdreverse_copy)
		- [`std::rotate`](#stdrotate)
		- [`std::shift_left`, `std::shift_right` (C++20)](#stdshift_left-stdshift_right-c20)
		- [`std::shuffle`](#stdshuffle)
		- [`std::sample`](#stdsample)
		- [`std::unique`](#stdunique)
	- [3. Algorytmy sortujące](#3-algorytmy-sortujące)
		- [`std::is_sorted`](#stdis_sorted)
		- [`std::is_sorted_until`:**`](#stdis_sorted_until)
		- [`std::sort`](#stdsort)
		- [`std::partial_sort`](#stdpartial_sort)
		- [`std::stable_sort`](#stdstable_sort)
		- [`std::nth_element`](#stdnth_element)
	- [4. Algorytmy partycjonujące](#4-algorytmy-partycjonujące)
	- [5. Algorytmy wyszukiwania binarnego](#5-algorytmy-wyszukiwania-binarnego)
	- [6. Inne operacje na posortowanych zbiorach](#6-inne-operacje-na-posortowanych-zbiorach)
	- [7. Operacje na zbiorach](#7-operacje-na-zbiorach)
	- [8. Operacje na kopcach (heap)](#8-operacje-na-kopcach-heap)
	- [9. Operacje znajdujące minimum/maximum](#9-operacje-znajdujące-minimummaximum)
	- [10. Operacje porównujące](#10-operacje-porównujące)
	- [11. Permutacje](#11-permutacje)
		- [`std::next_permutation`](#stdnext_permutation)
		- [`std::prev_permutation`](#stdprev_permutation)
	- [12. Operacje numeryczne](#12-operacje-numeryczne)
		- [`std::iota`](#stdiota)
		- [`std::accumulate`](#stdaccumulate)
		- [`std::inner_product`](#stdinner_product)
		- [`std::adjacent_difference`](#stdadjacent_difference)
		- [`std::partial_sum`](#stdpartial_sum)
		- [`std::reduce`](#stdreduce)

## Kontenery

* Kontenery przechowują dane określonego typu i same zarządza pamięcią tzn. w momencie jeśli np. wektor przestaje istnieć to on sam usuwa je z pamięci
* Powinno się preferować metody/algorytmy dedykowane konkretnemu kontenerowi nad metody globalne
	* Są one lepiej zoptymalizowane i przez to często szybsze
* `std::begin(vec)` i `std::end(vec)` jest bardziej uniwersalne bo działa też dla zwykłych tablic C
* Nie należy dziedzczyć po kontenerach z biblioteki STL bo nie mają one wirtualnych destruktorów
	* Lepiej zastosować mechanizm agregacji
	* Jeśli nasza klasa coś dodatkowo sobie trzyma to nasz wektor tego nie zwolni i beda wycieki pamieci

### 1. Kontenery sekwencyjne

#### `std::array`

* **Cache-friendly**
* Alokowany na stosie
* Dostęp do n-tego elementu - O(1)
	* Elementy leżą kolejno w pamięci
* Żeby funkcja mogła przyjmować jako argument `std::array` trzeba zrobić z niej funkcję szablonową:

```cpp
template <size_t T>
void print(const std::array<int, T>& a) {
    std::for_each(begin(a), end(a), [&a](int number){ std::cout << number << "\n"; });
}
```

#### `std::vector`

* **Chache-friendly**
* Alokowany dynamicznie na stercie
* Dostęp do n-tego elementu - O(1)
	* Elementy leżą kolejno w pamięci
* Wstawianie na koniec - w zamortyzowanym czasem stałym (O(1))
	* Bo możliwe, że dojdziemy do limitu capacity i będzie trzeba go przenieść gdzieś indziej
* Zjawisko inwalidacji iteratorów

#### `std::deque`

* Double-ended queue (dek)
* Podobny do wektora
* Wstawianie na początek i na koniec w zamortyzowanym czasie stałym (O(1))
* Dostęp do n-tego - O(1)
* Alokowany w NIECIĄGŁYM kawałku pamięci
* W momencie jak kończy się pamięć, rezerowany jest nowa pamięć i jest gdzieś w pamięci tablica wskaźników która przechowuje adresy tych wszystkich "kawałków" (stanowi to dodatkowy narzut)
* Przykład zastosowania: kolejka FIFO

#### `std::list`

* Każda lista ma:
	* Element HEAD, który wskazuje na 1-szy element listy
	* Element TAIL, który wskazuje na ostatni element listy
* Każdy element listy zawiera: dane, wskaźnik na poprzedni oraz wskaźnik na następny
* Dostęp do n-tego - O(n)
* Zalety: czas wstawiania i usuwania (O(1)), iteratory nie są inwalidowane
* Jako jedyny kontener ma swoją funkcję sortującą, wszystkie inne kontenery wymagają użycia algorytmu `std::sort`

**Kiedy `std::list` a kiedy `std::vector`?**

* `std::vector` ma szybki dostęp i modyfikację dowolnego elementu, ale powolne jest usuwanie (po usunięciu trzeba przesunąć wszystkie)
* `std::vector` po przekroczeniu capacity musi zaalokować nowe miejsce w pamięci i przenieść dane
* `std::list` ma wolniejszy dostęp (bo trzeba przeskoczyć X elementów), ale dodawanie i usuwanie nawet dużych ilości elementów jest szybkie

#### `std::forward_list`

* Elementy nie mają wskaźnika poprzedni
* Lista nie ma elementu TAIL
* Dostęp do n-ntego - O(n)
* Wstawianie i usuwanie O(1)

### 2. Kontenery asocjacyjne

Asocjacyjne (kolejność nie jest taka jak kolejność dodawania, służą do szybkiego wyszukiwania danych).

#### `std::set` / `std::multiset`

* Set przechowuje tylko unikalne wartości
* W multiset możliwe wystąpienie tej samej wartości
* Elementy sortowane podczas wstawiania
* Wszystkie elementy są const
* Wyszukiwanie i wstawianie O(logn)
* Dzięki temu że uporządkowany, stosuje się wyszukiwanie binarne

#### `std::map` / `std::multimap`

* Map przechowuje tylko unikalne wartości
* W multimap możliwe wystąpienie tej samej wartości
* Elementy sortowane podczas wstawiania
* Wszystkie elementy są const, można go tylko nadpisać
* Wyszukiwanie i wstawianie O(logn)
* Dzięki temu że uporządkowany, stosuje się wyszukiwanie binarne
* Odwołanie się przez operator [] do klucza (indeksu) który istnieje spowoduje nadpisanie klucza
* Dodanie klucza który istnieje korzystając z insert nic nie zrobi

#### `std::unordered_map` / `std::unordered_multimap` / `std::unordered_set` / `std::unordered_multiset`

* Kontenery nieuporządkowane (hash-mapy)
* Kontener jest zorganizowany w kubełki (buckets)
* Przy wyszukiwaniu lub wstawianiu uruchamiana jest funkcja hash
* Jest funkcja hash która zwraca numer kubełka w którym trzeba szukać danej wartości
	* Funkcja hash to naprzykład reszta z dzielenie. W przypadku %4 mamy 4 kubełki: 0, 1, 2, 3
* Biblioteka standardowa dostarcza funkcje hashujące dla podstawowych typów danych: int, std::string itd. Do typów własnych trzeba taką funkcję napisać samemu.
* W przypadku jeśli zbyt dużo wartości ląduje w jednym kubełku (`load_factor()` jest duży) trzeba wykonać `rehash()` po którym nastąpi nowy podział, np. na więcej kubełków (można ustawić `max_load_factor()` po którym następuje `rehash()`)

### 3. Adaptery

Domyślnie wszystkie adaptery używają kontenera deque.

#### `std::stack`

* Stos (LIFO)
* Operacje: `push()`, `pop()`, `top()`

#### `std::queue`

* kolejka (FIFO))
* Operacje: `pop()`, `push()`, `back()`, `front()`

#### `std::priority_queue`

* Kolejka priorytetowa
* Ściąga zawsze top (element z najwyższą wartością)
* Można to zmienić przy tworzeniu kolejki i funkcji `std::greater`
	* `std::prioritity_queue<int, std::vector<int>, std::greater<int>>`
* Operacje
  * `pop()` - ściąga z góry
  * `push()` - przy wstawianiu są elementy są sortowane, największa wartość trafia na górę

### 4. Inne

#### `std::wstring`

* Widestring
* Zajmuje 2 razy więcej miejsca od `std::string` - 16 bajtów

#### `std::vallaray`

* Specjalna tablica do której wrzuca się liczby, na której od razu wykonywane są jakieś działania

#### `std::tuple`

* Krotka
* Kontener na struktury, przechowuje typy.
* `std::tuple<int, std::string, double> t` i wtedy `t = {1, "hello", 2.25};
* Dostep do wartości przez `t[0]`, t[1]`
* `std::pair` to szczególny przypadek `std::tuple`

#### `std::bitset`

* Pole bitowe
* Alokowany na stosie

## Algorytmy

### 1. Algorytmy niemodyfikujące

#### `std::all_of`, `std::any_of`, `std::none_of`

* Operacje logicze  na całym kontenerze;
* Zwracają `true`/`false`, gdy spełniają określony predykat
* Przykładowe użycie

```cpp
std::vector<int> v {2, 4, 6, 8};

auto allEven = std::all_of(begin(v), end(v), [](int a){ return a % 2 == 0; });
```

#### `std::for_each`

* Odpowiednik zakresowej pętli `for` - dla każdego elementu kolekcji wykona określoną operację

#### `std::for_each_n`

* Jak `std::for_each` z tą różnicą, że używa jednego iteratora oraz ilości elementów (zamiast końcowego iteratora)

#### `std::count` / `std::count_if`
  
* Zliczanie wystąpień elementu w kolekcji (`std::count`);
* Zliczanie elementów w kolekcji spełniających kryteria (`std::count_if`);
* Przyjmuje funkcję, funkcję lambda lub funktor

#### `std::mismatch`

* Szuka zakresu elementów, a nie pojedynczego elementu;
obą:
* jeśli są identyczne - iteratory przejdą do końca;
* jeśli się różnią - zwróci iter. na różniące się elementy;
 
#### `std::find`

* Zwraca iterator na poszukiwany element
 
#### `std::find_if`
  
* Zwraca iterator na pierwszy napotkany element spełniający kryteria

#### `std::find_if_not`

* Zwraca iterator na pierwszy napotkany element, który nie spełnia podanego kryterium
		
#### `std::find_end`

* Zwraca iterator na ostatnie występienie elementu (za element można wstawić np mniejszy wektor)

#### `std::find_first_of`

* jako argumenty podajemy np. dwa wektory:
* pierwszy jest przeszukiwanszuka zakresu elementów, a nie pojedynczego elementu;
ą kolekcją,
* drugi argument to szukane elementy;
* funkcja zwróci iterator na pierwsze wystąpienie w 1 kontenerze,
	któregokolwiek elementu z drugiego kontenera;

#### `std::adjacent_find`

* Szuka dwóch elementów, które sąsiadują ze sobą i są identyczne
* Zwraca iterator na pierwszy z nich

#### `std::search`
  
* Szuka zakresu elementów, a nie pojedynczego elementu
* Zwraca iterator na pierwszy element poszukiwanego podciągu
  
#### `std::search_n`

* Nie szuka do końca kolekcji, podajemy przez ile elementów ma się odbyć przeszukiwanie

### 2. Algorytmy modyfikujące

#### `std::copy` / `std::copy_if`

* Skopiuje zakres do nowego zakresu lub kontenera
* Kontenery nie muszą być tego samego typu
* `std::copy_if` skopiuje elementy spełniające warunek

#### `std::copy_n`

* Jak `std::copy` ale skopiuje n wartości od begin()

#### `std::copy_backward`

* Kopiowanie w odwrotnej kolejności

#### `std::move`

* Przenosi elementy z jednej kolekcji do drugiej

#### `std::move_backward`
 
* Przenosi elementy z jednej kolekcji do drugiej, ale w odwrotnej kolejności (od końca)

#### `std::fill`

* Wypełnia każdy element w kontenerze podaną wartością

#### `fill_n`

* Wypełnia n elementów podaną wartością

#### `std::transform` (!)

* Transformuje zakres wejściowy -> zakres wyjściowy
* Może być konieczność użycia odpowiedniego insertera do kontenerów (np. do vectora `std::back_inserter`)

#### `std::generate` (!)

* Przyjmuje zakres wyjściowy i funktor
* Generuje dane do kontenera, przykład z użyciem lambdy

```cpp
std::generate (v.begin(), v.end(), [n=0]() mutable { return n++; });
```

* Mutable ściąga niejawnego consta ze zmiennych zdefiniowanych na liście przechwytującej
* Zmienne zdefiniowane na liście przechwytującej są zawsze typu auto

#### `std::generate_n`

* Jak `std::generate`, ale nie podajemy końca tylko n elementów

#### `std::remove, remove_if`

* Usuwa elementy z kontenera
* Usuwa pojedyncze elementy dla których funkcja lambda zwróci true

Erase-remove idiom

```cpp
v.erase(std::remove(v.begin(), v.end(), 5), v.end());
```

* Remove przesuwa wybrane elementy na koniec kontenera i zwraca iterator do początku zakresu w ktorym zaczynaja sie "te liczby"
* Erase usuwa elementy w podanym zakresie

W C++20 możliwe jest

```cpp
std::erase(v, 5));
```

#### `std::remove_copy`, `std::remove_copy_if`

* Kopiuje elementy z pominięciem elementów spełniających kryteriów
* Przykładowe użycie: usuwanie spacji ze zmiennej typu std::string
  
#### `std::replace`, `std::replace if`

* Zamienia elementy spełniające określone kryterium

#### `std::replace_copy`, `std::replace_copy_if`

* Jak `std::replace` oraz `std::replace_if` tylko kopiują wynik do nowego kontenera

#### `std::swap`

* Zamiana dwóch kontenerów ze sobą
* Muszą być takie same kontenery
* Wartości trzymane pod spodem zostaną ze sobą zamienione

#### `std::swap_ranges`

* Zamienia po zakresach
* Mogą być kontenery różnego rodzaju

#### `std::iter_swap`

* Zamień elementy na które pokazują dwa iteratory (mogą być różne kontenery)

#### `std::reverse`

* Odwraca zawartość kontenera

#### `std::reverse_copy`

* Analogicznie jak `std::reverse` tylko wynik zapisywany jest w nowym kontenerze

#### `std::rotate`

* Obracanie/przesuwanie kontenera względem elementu na który wskazuje iterator

`1 2 >3< 4 5` -> `>3< 4 5  1 2`

#### `std::shift_left`, `std::shift_right` (C++20)
  
* Jak rotate, podajemy o ile ma przesunąć oraz w którą stronę;

#### `std::shuffle`

* Mieszanie kolejności elementów w kolekcji

#### `std::sample`

* Wybierz n losowych wartości z naszego kontenera wejściowego

#### `std::unique`

* Przesuwa duplikaty na koniec kolekcji i zwraca iterator na miejsca gdzie zaczynają się duplikaty

### 3. Algorytmy sortujące

#### `std::is_sorted`

* Czy dany kontener jest posortowany
* Można podać lambdę / komparator

#### `std::is_sorted_until`:**`

* Zwraca iterator na ostatni posortowany element

#### `std::sort`

* Sortuje rosnąco
* Można przekazać komparator

#### `std::partial_sort`

* Sortuje pierwsze n elementów
	* Podajemy ilość elementów do posortowania
	* Resztę elementów pozostawia nieposortowaną;
	* **Pozostałe elementy mogą mieć zmienioną kolejność!**
	* przyjmuje 3 iteratory, sortuje od iteratora first do middle;
 
#### `std::stable_sort`

* Gdy sortujemy na przykład 4 pary i bierzemy tylko first do sortowania
	* Mamy gwarancję, że nie pomiesza kolejności ze względu na drugą wartość:

`(1,1) (1,2) (2,2) (2,1)` -> input;
`(1,1) (1,2) (2,2) (2,1)` -> stable sort
`(1,2) (1,1) (2,2) (2,1)` -> zwykły sort tak może zrobić

#### `std::nth_element`
* 
	* wybrany element będzie na swoim miejscu;
	* na lewo elementy będą mniejsze;
	* na prawo elementy będą większe;

### 4. Algorytmy partycjonujące

* dzielą nam zbiór wejściowy na 2 części -> część lewą i część prawą;
* przekazujemy lambdę, która przekazuje kryteria partycjonowania;
* np podział na elementy parzyste i nieparzyste

* **partition:**
* 
	* wrzucamy lambde z warunkiem, dla pierwszego zakresu:
	(parzyste true, znajdą się w 1 kontenerze, nieparzyste w 2);
	* kolejność po partycjonowaniu nie zostanie zachowana;
	* 
* **stable_partition:**
* 
	* jak partition, ale nie zostanie zamieniona względna kolejność elementów w tej kolekcji;
	* 
* **partition_point:**
* 
	* zwraca iterator na granicę partycjonowania (pierwszy element nie spełniający predykatu)
	* 
* **is_partitioned:**

	* zwraca *true* lub *false*;


### 5. Algorytmy wyszukiwania binarnego


**Algorytmy partycjonujące:**

	* .find jest gotowe w mapie lub w zbiorze;
	* binary search można użyć przy użyciu algorithm
	warunkiem jest posiadanie posortowanego kontenera

* **binary_search:**

W przypadku powtarzających się elementów możemy skorzystać z:

* **lower_bound** oraz **upper_bound**:
* 
	* zwraca odpowiednio:
		* iterator na pierwszy powtarzający się element
		* iterator na element za ostatnim powtarzającym się:
		`1 2* 2 3* 3 4` -> w przypadku liczby 2;

* **equal_range:**
* 
	* zwraca zakres elementów pasujących do podanego klucza;
	* zwraca parę lower i upper bound (first -> lower, second -> upper)
 
### 6. Inne operacje na posortowanych zbiorach

* std::merge
* 
	* przyjmuje 2 zakresy i trzeci do którego dane zostaną wstawione;
	* zachowuje kolejność, będzie sortować dane
	(można podać własny komparator);

* **inplace_merge:**
* 
	* dołącza drugi zakres do pierwszego;

### 7. Operacje na zbiorach

* **includes**:
* 
	* mówi nam czy jeden zbiór zawiera się w drugim
	(czy występuje część wspólna)

* **set_union:**
* 
	* suma zbiorów -> elementy z jednego i drugiego zbioru:
		* w wyniku działania set_union usunięte zostaną duplikaty;
		* 
* **set_intersection:**
* 
	* zwraca część wspólną zbiorów;
	* 
* **set_difference:**
* 
	* zwraca różnicę pomiędzy zbiorami;
	* kolejność argumentów ma znaczenie;
	* 
* **set_symetric_difference:**

	* suma wszystkiego co nie jest częścią wspólną
	(wyklucza część wspólną);

### 8. Operacje na kopcach (heap)

* **push_heap:**

	* dodawanie do kopca;
  
* **pop_heap:**

	* ściąganie z kopca;
  
* **make_heap:**

	* sortowanie, aby elementy odpowiadały strukturze kopca;
  
* **sort_heap:**

	* sortowanie kontenera;
  
* **is_heap:** 

	* czy mamy strukturę kopca w kontenerze;

* **is_heap_until:**

* do kiedy kontener spełnia strukturę kopca;

### 9. Operacje znajdujące minimum/maximum

* **min:**

	* zwraca najmniejszą **wartość**

* **max:** 

	* zwraca największą **wartość**
  
* **minmax:**

	* zwraca parę min i max (**wartość**)
  
* **min_element:**

	* zwraca iterator na najmniejszy element

* **max_element:**

	* zwraca iterator na największy element
  
* **minmax_element:**

	* zwraca parę iteratorów na min oraz na max
  
* **clamp:**

* spłaszczenie wartości do podanych granic:
* podajemy wartość min i max (np 3 i 5)
* wszystko mniejsze od 3 -> będzie 3 
* wszystko powyżej 5 -> będzie 5
* wartości pośrednie nie ulegną zmianie

### 10. Operacje porównujące

* **lexicographical_compare:**

	* zwraca true jeśli pierwsza permutacja będzie mniejsza od drugiej
	* przykład kolejnych permutacji:  abc, acb, bac, bca, cab, cba

 * **equal:**
  
 	* zwraca wartość logiczną czy dwa zbiory są sobie równe
 	* czy elementy są takie same (np w deque i vectorze)
 	* 
 * **compare_3way:** *(C++20)*
  
* dziś mamy 6 operatorów logicznych:  <, <=, >, >=, ==, !=
* dojdzie 7-my operator 3 way: **<=>** (space ship operator :Ð)
zwróci jedną z 3 wartości:
	* mniejszy od 0 jeśli pierwszy element jest mniejszy
	* 0 jeśli są takie same
	* większy od 0 jeśli drugi element jest mniejszy
 * wykorzystać będzie można **std::compare_3way** lub **operator <=>**

### 11. Permutacje

#### `std::next_permutation`

* Modyfikuje kontener
* Zwraca `true`/`false` w zależności czy udało się wygenerować kolejną permutację
* Przyjmuje kontener (np. `std::string` lub kontener z liczbami)
* Chcąc sprawdzić wszystkie permutacje należy najpierw posortować kontener

```cpp
std::list<int> list {3, 4, 2}
list.sort();

do {
	/*	*/
} while (std::next_permutation(begin(list), end(list)));
```

#### `std::prev_permutation`

* Zwraca poprzednią permutację

### 12. Operacje numeryczne

#### `std::iota`

* Algorytm generujący ciąg rosnący o 1

```cpp
std::vector<int> v(10);		// reserve nie zadziała
std::iota(begin(v), end(v), 0);
```

#### `std::accumulate`

* Sumowanie elementów z kontenera
* Może przyjmować binary operator, który powie, że na przykłąd zamiast sumować elementy powinien je mnożyć (pierwszy argument to wartość początkowa (init))
* sprawdź również **std::reduce**

#### `std::inner_product`

* iloczyn skalarny:
`std::vector<int> a{0, 1, 2, 3};`
`std::vector<int> b{5, 4, 2, 3};`
	*output:*  `0*5 + 1*4 + 2*2 + 3*3`

#### `std::adjacent_difference`

* różnica pomiędzy sąsiadującymi elementami:
*input*: 	`10,   8,   10,   6`
*output:* 	`10, -2, 2, -4`

#### `std::partial_sum`

* Sumuje od początku do elementu na którym jesteśmy
* *input*: `10, 8, 10, 6` 
* *output*: `10, 18, 28, 34`

#### `std::reduce`

* Tak jak `std::accumulate`, ale wielowątkowy (`std::execution::par`)