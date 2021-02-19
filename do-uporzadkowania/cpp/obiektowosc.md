# Obiektowość <!-- omit in toc -->

- [Abstrakcja](#abstrakcja)
- [Enkapsulacja](#enkapsulacja)
- [Dziedziczenie](#dziedziczenie)
- [Polimorfizm](#polimorfizm)

## Abstrakcja

* Wyodrębnienie i uogólnienie najważniejszych cech problemu i wyrażeniu rozwiązania właśnie w obrębie tych cech
* To model obiektowy: struktura klas oraz interfejsy (publiczne API)

## Enkapsulacja

* Settery i Gettery
* Settery umożliwiają walidację danych
* Inne
  * Unnamed namespace (forma enkapsulacji) - można stworzyć namespace bez nazwy - wtedy wszystko w nim zawarte będzie prywatne w obrębie jednego pliku

Public - można odwoływać się zarówno z wewnątrz klasy jak i z zewnątrz
Protected - można odwoływać się tylko z wewnątrz klasy
Private - zablokowany dostęp

Dziedziczenie ->  | PUBLIC | PROTECTED | PRIVATE
------------ | ------------- | ------------- | -------------
Content from cell 1 | Content from cell 2 | Second Header | -------------
Content in the first column | Content in the second column | Second Header | -------------
Content in the first column | Content in the second column | Second Header | -------------

## Dziedziczenie

* Kolejność wołania konstruktrów i destruktorów
  * Konstruktory wywołane są począwszy od klasy bazowej (tej najwyżej w hierarchi w dół)
    * Przed wywołaniem konstruktora klasy bazowej wołane są konstruktory memberów danej klasy (jeśli są jakieś)
  * Destruktory wołane są w odwrotnej kolejności do konstruktorów
* Problem diamentowy (rozwiązanie - wirtualne dziedziczenie)
* Domyślne dzidziczenie klasy (`class`) ze struktury (`struct`) jest prywatne
* Domyślne dzidziczenie struktury (`struct`) z klasy (`class`) jest publiczne

## Polimorfizm

* Wirtualne funkcje
* Funkcje czysto wirtualne (`=0`)
* Klasy abstrakcyjne (klasa z przynajmniej 1 funkcją czysto wirtualną)
* Implementacja polimorfizmu ()
  * Konstruktor klasy pochodnej nadpisuje pola klasy bazowej w `vtable` w momencie jeśli funkcja pochodna nadpisuje jakąś metodę wirtualną
  * Każda klasa ma swoją `vtable`
  * Każdy obiekt klasy (który albo ma w sobie przynajmniej jednąfunkcję wirutalną albo uczestniczy w dziedziczeniu przy użyciu polimorfizmu) ma w sobie dodatkowe pole - wskaźnik `vptr`
  
`vtable` - tablica funkcji wirtualna
`vptr` - wskaźnik na tablicę funkcji wirtualnych

