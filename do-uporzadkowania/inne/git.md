# Git

1. [Poprawki do ostatniego commit-a](#1-Poprawki-do-ostatniego-commit-a)
2. [Porównanie katalogu roboczego z ostatnią rewizją](#2-porownanie-katalogu-roboczego-z-ostatnia-rewizja)
3. [Różnica między poczekalnią, a ostatnią rewizja](#3-roznica-miedzy-poczekalnia-a-ostatnia-rewizja)
4. [Przywracanie zmian](#4-przywracanie-zmian)
5. [Czyszczenie katalogu roboczego z niechcianych plików](#5-Czyszczenie-katalogu-roboczego-z-niechcianych-plikow)
6. [Wprowadzanie zmian z konkretnego commit-a](#6-Wprowadzanie-zmian-z-konkretnego-commit-a)


## 1. Poprawki do ostatniego commit-a

Zamiast nowy commit-a:
* Wprowadzamy zmiany w plikach
* `git add .`
* `git commit --amend --no-edit`

**Nie wolno używać w sytuacji, że nad repozytorium pracuje ktoś jeszcze oprócz nas!**

## 2. Porownanie katalogu roboczego z ostatnia rewizja

`git diff`

## 3. Roznica miedzy poczekalnia, a ostatnia rewizja

`git diff --staged`<br>
`git diff --cached`

## 4. Przywracanie zmian

### 4.1 git checkout

Operacja polegająca na przesunięciu wskaźnika HEAD na wskazany commit.

**Przywracanie pojedynczego pliku**

`git checkout <COMMIT> <PLIK>`

**Przywracanie stanu repozytorium**

`git checkout <COMMIT>`

W ten sposób trafiamy do trybu tzw. odłączonej głowy (`detached HEAD`). Można przeglądać projekt i wprowadzać zmiany. W celu zapisana zmian tworzymy nową gałąź:

`git checkout -b <NOWY_BRANCH>`

### 4.2 git revert

Operacja polegająca na **odwróceniu zmian** z wybranego commit-u i zapisaniu ich jako nowy commit.

`git revert <COMMIT>`

**Wybieramy commit który chcemy wycofać, a nie ten do którego chcemy się cofnąć!**

`git revert HEAD~3` - (cofa o 3 rewizje do tyłu)

### 4.3 git reset

Przywraca zmiany w repozytorium do wskazanego punktu w historii.

**Uwaga! To polecenie modyfikuje historię Git'a!**
**W związku z tym powinno się je stosować tylko do commit-ów, które nie zostały opublikowane komendą `git push`.**

`git reset --mixed <COMMIT>`<br>
> `--mixed` - domyślne (nie trzeba pisać), wszystkie zmiany wprowadzone po commicie do którego się cofamy zostaną w katalogu roboczym<br>
> `--soft` - wszystkie zmiany (...) zostaną przeniesione na `stage`<br>
> `--hard` - wszystkie zmiany (...) zostaną usunięte (!)

## 5. Czyszczenie katalogu roboczego z niechcianych plikow

Polecenie służące do usuwania z katalogu roboczego nieśledzonych plików i katalogów.

`git clean -ndf`
> `-n` - wyświetla listę plików<br>
> `-nd` - wyświetla listę plików i katalogów do usunięcia<br>
> `-i` - tryb interaktywny (pyta o każdy plik/katalog)<br>
> `-f` - usuwa bez oczekiwania na potwierdzenie

**Przykłady**

`git clean -ndf` - usuwa wszystkie pliki i katalogi
`git clean -f` - usuwa wszystkie pliki

## 6. Wprowadzanie zmian z konkretnego commit-a

Przydatne jeśli chcemy wprowadzić zmiany z konkretnego commit-a znajdującego się na przykład na innym branchu. Nie chcemy robić `merge` ani `rebase` bo to wprowadzi zmiany ze wszystkich commit-ów na danym branchu.

`git cherry-pick <COMMIT_1> <COMMIT_2>` - to stworzy nowy commit na aktywnym branchu

`git cherry-pick <COMMIT_1> -n` - to naniesie zmiany na przestrzeń roboczą ale nie stworzy commit-a