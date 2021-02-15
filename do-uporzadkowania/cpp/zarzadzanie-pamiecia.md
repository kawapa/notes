# Zarządzanie pamięcią

1. [Valgrind](#1-valgrind)

## 1. Valgrind

Valgrind to debugger pod Linuxa. Jego główną funkcją jest wykrywanie problemów z zarządzaniem pamięcią (wykrywa na przykłąd wycieki pamięci). Domyslnie valgrind odpala moduł `memcheck`.

* Valgrind posiada jeszcze moduł do wielowątkowości (`helgrind`), który wykrywa wyścigi (data races)

### 1.1 Działanie

> `g++ main.cpp -g`<br>
> `valgrind ./a.out`

Dodatkowo, Valrind może wskazać w której linii pojawia się wyciek:
> `valgrind --leak-check=full ./a.out`