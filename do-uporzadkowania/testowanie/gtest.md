- [Google Test](#google-test)
  - [Przydatne asercje](#przydatne-asercje)
  - [Organizacja testow](#organizacja-testow)
    - [Opcja 1 - najprostsza](#opcja-1---najprostsza)
    - [Opcja 2 - ze struktura na czesci wspolne](#opcja-2---ze-struktura-na-czesci-wspolne)
    - [Opcja 3 - testy parametryczne](#opcja-3---testy-parametryczne)
  - [Najważniejsze przełączniki do testów](#najważniejsze-przełączniki-do-testów)
- [Google Mock](#google-mock)
- [Fake](#fake)
- [Stub](#stub)
- [Mock](#mock)
- [Mockowanie metod](#mockowanie-metod)
  - [Nowy sposób](#nowy-sposób)
  - [Stary sposób](#stary-sposób)
- [Ustawianie spodziewanych oraz zachowań](#ustawianie-spodziewanych-oraz-zachowań)

## Google Test

### Przydatne asercje

* `Fatal assertion` pierwszy oblany test powoduje przerwanie testowania
* `Non-fatal assertion` nie powoduje przerwania testów

| Fatal assertion                | Nonfatal assertion             | Verifies              |
| ------------------------------ | ------------------------------ | --------------------- |
| `ASSERT_TRUE(condition);`      | `EXPECT_TRUE(condition);`      | condition is true     |
| `ASSERT_FALSE(condition);`     | `EXPECT_FALSE(condition);`     | condition is false    |
| `ASSERT_EQ(val1, val2);`       | `EXPECT_EQ(val1, val2);`       | val1 == val2          |
| `ASSERT_NE(val1, val2);`       | `EXPECT_NE(val1, val2);`       | val1 != val2          |
| `ASSERT_LT(val1, val2);`       | `EXPECT_LT(val1, val2);`       | val1 < val2           |
| `ASSERT_LE(val1, val2);`       | `EXPECT_LE(val1, val2);`       | val1 <= val2          |
| `ASSERT_GT(val1, val2);`       | `EXPECT_GT(val1, val2);`       | val1 > val2           |
| `ASSERT_GE(val1, val2);`       | `EXPECT_GE(val1, val2);`       | val1 >= val2          |
| `ASSERT_THAT(value, matcher);` | `EXPECT_THAT(value, matcher);` | value matches matcher |

<br>

| Fatal assertion                            | Nonfatal assertion                         | Verifies                                        |
| ------------------------------------------ | ------------------------------------------ | ----------------------------------------------- |
| `ASSERT_THROW(statement, exception_type);` | `EXPECT_THROW(statement, exception_type);` | statement throws an exception of the given type |
| `ASSERT_ANY_THROW(statement);`             | `EXPECT_ANY_THROW(statement);`             | statement throws an exception of any type       |
| `ASSERT_NO_THROW(statement);`              | `EXPECT_NO_THROW(statement);`              | statement doesn't throw any exception           |

### Organizacja testow

* Nie ma Given-When-Then

#### Opcja 1 - najprostsza

* `TEST(TestSuiteName, TestName)`
    * `TestSuiTEST_FteName` - nazwa ogólna dla całego zestawu testów (odpowiednik tagów z Catch2, można potem odpalać testy o wybranej nazwie)<br>
    * `TestName` - nazwa konkretnego testu

```cpp
TEST(ClassUnderTestSuite, callMeShouldAlwaysReturn42) {
    // GIVEN
    ClassUnderTest cut{};
    auto expected = 42;

    // WHEN
    auto result = cut.callMe();

    // THEN
    ASSERT_EQ(result, expected);
}
```

#### Opcja 2 - ze struktura na czesci wspolne

* `TEST_F(TestFixtureName, TestName)`
    * `TestFixtureName` - test z dzielonymi danymi
    * `TestName`

```cpp
#include "gtest/gtest.h"

#include <algorithm>
#include <string>

struct SortTestixture : public ::testing::Test {
    template <class Type>
    void sortAndCheck(Type& input,
                      const Type& expected) {
        
    std::sort(begin(input), end(input));
    ASSERT_EQ(input, expected);
    }
};

TEST_F(SortTestixture, GiveANonemptyShortString) {
    std::string input = "text";
    std::string expected = "ettx";
    sortAndCheck(input, expected);
}

TEST_F(SortTestixture, GiveANonemptyLongString) {
    std::string input {"zcacazzda"};
    std::string expected {"aaaccdzzz"};

    sortAndCheck(input, expected);
}
```

#### Opcja 3 - testy parametryczne

```cpp
#include <gtest/gtest.h>
#include "Parentheses.hpp"

struct ParenthesesClassFixture : public testing::TestWithParam<std::pair<std::string, bool>> {
    Parentheses p;

    void checkIsBalanceMethod(const std::string& string, bool expectedResult) {
        EXPECT_EQ(p.isBalanced(string), expectedResult);
    }
};

TEST_P(ParenthesesClassFixture, givenEmptyStringWhenIsBalancedIsCalledThenResultIsTrue) {
    auto [string, expectedResult] = GetParam();
    checkIsBalanceMethod(string, expectedResult);
}

INSTANTIATE_TEST_SUITE_P(MyInstantiationName,
                         ParenthesesClassFixture,
                         testing::Values(
                             std::make_pair("", true),
                             std::make_pair("{{)(}}", false),
                             std::make_pair("({)}", false),
                             std::make_pair("[({})]", true),
                             std::make_pair("{}([])", true),
                             std::make_pair("{()}[[{}]]", true),
                             std::make_pair("{(([]))]((", false)
                         )
);
```

### Najważniejsze przełączniki do testów

* `--gtest_filter=<NAZWA_TESTU>` - odpalamy tylko testy o danej nazwie
* `--gtest_repeat=<LICZBA>` - testy wykonają się ileś razy (istotne przy badaniu undefined behaviour)
* `--gtest_shuffle` - wymieszaj testy. Testy nie są odpalane w takiej kolejności jak w pliku cpp

## Google Mock

Sposób izolowania testów przez usunięcie zależności i zastąpieniu ich pustymi obiektami.

Testowanie klasy z dostępem do bazy danej nie powinno polegać na faktycznych zmianach w bazie danych.

Testy też nie powinny oblewać bo nie ma połączenia z bazą.

Mokowanie polega na dziedziczeniu i nadpisywaniu testowanych metod (nadpisywana metoda może nic nie robić LUB ...).

Można sprawdzić:
    * czy metoda zostala wywolana
    * , ile razy,
    *  z jakimi parametrami.





## Fake

* Posiada uproszczoną implementację
* Nie nadaje się na produkcję
* Przykład: baza danych

## Stub

* Odpowiada przy użyciu predefiniowanych odpowiedzi
* Może być też 


## Mock
* Rzuca wyjątki


## Mockowanie metod

### Nowy sposób

```cpp
MOCK_METHOD(ReturnType, MethodName, (Arguments...));
```

* ReturnType musi być w nawiasach jeśli występuje w nim przecinek, na przykład `std::map<int, int>`
* Parametry zawsze w nawiasach, nawet jeśli jest jeden
* Możliwe dodanie opcjonalnego 4-tego argumentu (Specs...), w który dodajemy słowa kluczowe typu `const`, `override`, `noexcept`.

Przykłady:

```cpp
int sum(int a, int b);

MOCK_METHOD(int, sum, (int, int));
```
```cpp
std::map<int, int> foo(float x);

MOCK_METHOD((std::map<int, int>), foo, (float));
```
```cpp
int sum(int a, int b) const;

MOCK_METHOD(int, sum, (int, int), (const));
```

### Stary sposób

```cpp
MOCK_METHOD<LICZBA_ARGUMENTOW>(ReturnType, MethodName, (Arguments...));
```

* Liczba argumentów od 0 do 10

Przykłady:

```cpp
int sum(int a, int b);

MOCK_METHOD2(sum, int(int, int));
```
```cpp
int sum(int a, int b) const;

MOCK_CONST_METHOD2(sum, int(int, int));
```

## Ustawianie spodziewanych oraz zachowań

Makra:

* `ON CALL` - ustawia zachowanie metody w momencie jej wywołania
  * Rzadko stosowane
* `EXPECT_CALL` - `ON_CALL` + ustawia "expectations"
  * Jeśli test nie spełnia tych "expectations", test nie przechodzi
* `ACTION` - powoduje konkretną akcję

Przykład - sprawdzanie ilości wywołań:

```cpp
EXPECT_CALL(someObject, someMethod).Times(2)
```

Przykład - sprawdzanie argumentu:

```cpp
EXPECT_CALL(someObject, someMethod("Value I expect to be passed"));
```

Przykład - wywołanie akcji

```cpp
ACTION(ThrowSomeException) {
    throw std::runtime_error("Dummy error");
}

EXPECT_CALL(someObject, someMethod()).WillOnce(ThrowSomeException());
```

Ustawianie akcji / zwracanej wartości:
* `WillOnce`
* `WillRepeatedl`
* `WillByDefault`
* `Return`
* `ReturnRef`

Przykład - `Return`

```cpp
EXPECT_CALL(someObject, someMethod()).WIllRepeatedly(Return(6));
```

`testing::_`  zastępuje każdą wartość