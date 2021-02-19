# GDB

## Wstęp


```bash
> apt install gdb -y
> g++ main.cpp -ggdb3

// -ggdb3 jest lepsze niż samo -g
// gdb będzie wyświetlał więcej przydatnych informacji

> gdb --tui ./a.out

// ... ALBO 

> gdb ./a.out       (i potem ctrl + x + a

// TUI - Text User Interface
```


## Komendy

* `gdb --args a.out arg1 arg2` - uruchomienie programu z argumentami

### Podstawowe


* `start` - uruchomienie programu w trybie debuggera z domyślnym breakpoint`em na pierwszej linii main'a<br>
* `run` - uruchomienie programu
* `b 10` - ustawienie breakpoint'a na linii 10
* `continue` - przejście do kolejnego breakpoint'a
* `print VARIABLE` - wyświetla aktualny stan zmiennej
---
* `s` - wejście do funkcji (albo `step`)
* `n` - przejście do kolejnej linii (albo `next`)
* `ctrl + l` - wyczyszczenie terminala (czasem stdout z aplikacji zalewa ekran lub miesza się z komendami GDB)
* `watch foo` - zatrzymuje w momencie gdy foo jest modyfikowane
* `watch -l foo` - obserwuje lokalizację w pamięci
* `rwatch foo` - zatrzymuje gdy wartość foo jest odczytywana
* `watch foo thread 3` - zatrzymuje gdy wątek 3 modyfikuje 3
* `watch foo \if foo > 10` - zatrzymuje gdy foo 10
---
* `quit` - wyjście z GDB

### Wątki

* `info threads` - pokazuje wykorzystywane wątki
* `thread apply all bt` - backtrace
* `thread apply all bt full` - backtrace łacznie z lokalnymi zmiennymi 



### Bibliografia

* [CppCon 2018: Greg Law“Debugging Linux C++"](https://www.youtube.com/watch?v=V1t6faOKjuQ)