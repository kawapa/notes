# Catch2

* Catch2 to biblioteka nagłówkowaa , którą wystarczy załączyć za pomocą `#include`
* Przykład 1:
* 
```c++
#define CATCH_CONFIG_MAIN  // This tells Catch to provide a main() - only do this in one cpp file
#include "catch.hpp"

unsigned int Factorial( unsigned int number ) {
    return number <= 1 ? number : Factorial(number-1)*number;
}

// TEST_CASE("NAZWA", opcjonalne TAGI);
// Można wtedy odpalać tylko testy z danym tagiem
// W Catch2 w przeciwieństwie do GTest, nazwy mogą mieć spacje
TEST_CASE( "Factorials are computed", "[factorial]" ) {
    REQUIRE( Factorial(1) == 1 );
    REQUIRE( Factorial(2) == 2 );
    REQUIRE( Factorial(3) == 6 );
    REQUIRE( Factorial(10) == 3628800 );
}
```

* Zazwyczaj testy do każdej klasy znajdują się w osobnym pliku `*.cpp` (np. `Class-ut.cpp`)
* Przykład 2:

```cpp
TEST_CASE( "vectors can be sized and resized", "[vector]" ) {

    std::vector<int> v( 5 );

    REQUIRE( v.size() == 5 );
    REQUIRE( v.capacity() >= 5 );

    SECTION( "resizing bigger changes size and capacity" ) {
        v.resize( 10 );

        REQUIRE( v.size() == 10 );
        REQUIRE( v.capacity() >= 10 );
    }
    SECTION( "resizing smaller changes size but not capacity" ) {
        v.resize( 0 );

        REQUIRE( v.size() == 0 );
        REQUIRE( v.capacity() >= 5 );
    }
    SECTION( "reserving bigger changes capacity but not size" ) {
        v.reserve( 10 );

        REQUIRE( v.size() == 5 );
        REQUIRE( v.capacity() >= 10 );
    }
    SECTION( "reserving smaller does not change size or capacity" ) {
        v.reserve( 0 );

        REQUIRE( v.size() == 5 );
        REQUIRE( v.capacity() >= 5 );
    }
}
```

* `SECTION` jest opcjonalne

#### 3. Przydatne asercje

* `REQUIRE(expression)`
    * Jeśli asercja `REQUIRE` zwróci `false`, reszta testów następujących później się **nie wykona**
* `CHECK( expression )`
    * W odróżnieniu od `REQUIRE`, reszta testów mimo wszystko się wykona
    * Analogicznie następne asercje rozpoczynające się od `CHECK_`
* `REQUIRE_FALSE(expression)`
* `CHECK_FALSE(expression)`
    * W odróżnieniu od `REQUIRE_FALSE`
* `REQUIRE_NOTHROW(expression)`
    * Wyrażenie `expression` nie rzuci wyjątku
* `CHECK_NOTHROW(expression)`
* `REQUIRE_THROWS_AS(expression, exception type)`
    * Wyrażenie `expression` rzuci konkretny wyjątek (`exception type`)
* `CHECK_THROWS_AS(expression, exception type)`

**Matchery:**
* `REQUIRE_THAT(lhs, matcher expression)`
* `CHECK_THAT(lhs, matcher expression)`

* String matchers
    * **Catch::Matchers::**StartsWith
    * **Catch::Matchers::**EndsWith
    * **Catch::Matchers::**Contains
    * **Catch::Matchers::**Equals
    * **Catch::Matchers::**Matches
* Vector matchers
    * **Catch::Matchers::**Contains which checks whether a specified vector is present in the result
    * **Catch::Matchers::**VectorContains which checks whether a specified element is present in the result
    * **Catch::Matchers::**Equals which checks whether the result is exactly equal (order matters) to a specific vector
    * **Catch::Matchers::**UnorderedEquals which checks whether the result is equal to a specific vector under a permutation


Odpalając binarkę z testami możemy dorzucić dodatkowe flagi np.:

`./a.out -s` pokaże szczegóły testów, które się powiodły
`./a.out [tag1]` odpali testy opatrzone [tag1]


`./a.out --help` pokaże więcej opcji


STYLE PISANIA TESTÓW W CATCH2

```cpp
TEST_CASE("UNIKALNA NAZWA", "[tag1], [tag2]") {
    // Część wspólna na stworzenie obiektu
    
    SECTION("UNIKALNA NAZWA") {
    // Asercje porównujące
    }

    SECTION("UNIKALNA NAZWA") {
    // Asercje porównujące
    }
}
```

Sekcje są opcjonalne (można od razu pisać asercje), ale dzięki nim kod jest krótszy.

LEPSZY STYL PISANIA TESTÓW - BDD Behaviour Driven Development

```cpp
SCENARIO("NAZWA", "[tag1], [tag2]") {
    GIVEN("Mamy sobie obiekt") {
        // Stworzenie obiektu

        WHEN("Jeśli zrobimy na nim") {
            // Coś robimy na obiekcie
            
            THEN("Obiekt powinien się zmienić w taki sposób") {
                // Asercje
            }
        }
    }
}
```

Można wykorzystać dodatkowo `AND_GIVEN` i dowolnie zagnieżdżać je.

```cpp
SCENARIO("NAZWA", "[tag1], [tag2]") {
    GIVEN("To mamy otrzymać") {
        // Stworzenie tego co ma zwracać algorytm

        AND_GIVEN("U") {
            // Stworzenie przypadku testowego

            WHEN("Jeśli zrobimy na nim") {
                // Coś robimy na obiekcie
                
                THEN("Obiekt powinien się zmienić w taki sposób") {
                    // Asercje
                }
            }
        }

        AND_GIVEN("U") {
            // Stworzenie kolejnego przypadku testowego

            WHEN("Jeśli zrobimy na nim") {
                // Coś robimy na obiekcie
                
                THEN("Obiekt powinien się zmienić w taki sposób") {
                    // Asercje
                }
            }
        }
    }
}
```