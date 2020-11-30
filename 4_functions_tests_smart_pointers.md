### Funkcje, testy, inteligentne wskaźniki
#### Przestrzenie nazw
Chronią one przed konfliktami nazw.\
Domyślnie wszystkie symbole są deklarowane w globalnej przestrzeni nazw. Aby zadeklarować symbol w innej przestrzeni nazw należy użyć poniższej składni:
```cpp
namespace TestNamespace {
    // some symbol
    // another symbol
    class TestClass {
    };
}
```
W następujący sposób używa się obiektów znajdujących się w danej przestrzeni nazw:
```cpp
TestNamespace::TestClass testObject;
```
Alternatywą jest użycie `using TestNamespace::TestClass` żeby zaimportować określony symbol z danej przestrzeni nazw lub `using namespace TestNamespace` żeby zaimportować wszystkie symbole z danej przestrzeni nazw, ale im więcej symboli się importuje, tym większa szansa na pojawienie się konfliktów nazw.

#### Wskaźniki do funkcji
Programowanie funkcyjne jest paradygmatem programowania kładącym nacisk na funkcje i niezmienniczość danych. Jednym z głównych założeń programowania funkcyjnego jest możliwość przekazywania funkcji jako argumentów innych funkcji.\
Jednym ze sposobów przekazywania funkcji jako parametrów jest przekazywanie wskaźników do funkcji. Ponieważ funkcje, tak jak i inne obiekty, zajmują pewien obszar pamięci, to można wskazać na ten obszar pamięci korzystając ze wskaźników.\
Wskaźniki do funkcji deklaruje się w następujący sposób:
```cpp
return-type (*pointer-name)(art-type1, arg-type2, ...);
```
Aby uzyskać adres funkcji należy użyć operatora `&` lub można użyć samej nazwy funkcji (bez argumentów i bez nawiasów okrągłych).

#### Wyrażenia lambda
Wyrażenia lambda służą do tworzenia nienazwanych obiektów funkcyjnych.\
Wyrażenia lambda mają następującą postać:
```cpp
[captures] (parameters) modifiers -> return-type {body};
```
gdzie:
* `captures` - zmienne których można użyć w wyrażeniu (a zdefiniowane poza wyrażeniem)
* `parameters` - argumenty wywołania (opcjonalne)
* `body` - ciało wyrażenia 
* `modifiers` - elementy jak `constexpr`, `noexcept` itd. (opcjonalne)
* `return-type` - typ zwracany przez wyrażenie (opcjonalne)
np.
```cpp
[](int x) { return x*x; };
```

#### std::function
`std::function` z pliku `<functional>` to generyczny wskaźnik na funkcję, można do niego przypisać funkcję statyczną, obiekt funkcyjny (obiekt który można wywołać) lub lambdę.\
Deklaracja obiektu function wygląda w następujący sposób:
```cpp
std::function<return-type(arg-typ1, arg-type2,...>
```
np.
```cpp
std::function<int(int)> func;
// func = ...;
func();
```

#### Testowanie kodu
Jednym ze sposobów testowania kodu jest pisanie testów jednostkowych (unit tests). Testy jednostkowe weryfikują czy dana jednostka kodu - funkcja czy klasa - zachowuje się tak jak tego oczekuje programista. 

##### Google Test
Google Test to jedna z wielu bibliotek pozwalających na pisanie testów jednostkowych.\
Oficjalna strona Google Test to [github.com/google/googletest](github.com/google/googletest) a jednym ze sposobów instalacji jest wykonanie poniższych kroków:
```cpp
git clone https://github.com/google/googletest
cd googletest
mkdir build
cd build
cmake .. -DCMAKE_CXX_STANDARD=17
make
make install
```
Aby móc korzystać z biblioteki Google Test należy dołączyć plik nagłówkowy `#include <gtest/gtest.h>"` oraz podczas kompilacji dołączyć biblioteki `gtest` i `gtest_main`:
```cpp
g++ -std=c++2a -lgtest -lgtest_main test.cpp
```
Testy jednostkowe definiuje się wewnątrz makra `TEST`:
```cpp
#include <gtest/gtest.h>

TEST(<test_case_name>, <test_name>) {
  // Unit test here 
}
```
Jeden Test Case może zawierać jeden lub więcej testów.\
Google Test samo tworzy metodę main wywołującą wszystkie testy jednostkowe znajdujące się w kompilowanym pliku.\
Do sprawdzania poprawności kodu używa się asercji. Przykładowo poniższy kod to test jednostkowy sprawdzający poprawne działanie operatora dodawania:
```cpp
TEST(ArithmeticTest, AddingTest) {
    auto x = 1, y = 3;
    auto sum = x + y;
    ASSERT_EQ(4, sum);
}
```

#### Smart pointers
Wskaźniki inteligentne (smart pointers) pozwalają na automatycznę usuwanie zasobów zajmowanych przez obiekty. Smart pointer jest właścicielem wskazywanego obiektu, więc wraz z usunięciem wskaźnika usuwany jest także wskazywany obiekt.\
Jednym z przykładów inteligentnych wskaźników jest `unique pointer` z pliku nagłówkowego `memory`. Unique pointer zapewnia wyłączność przechowywanego obiektu (nie można wykonywać jego kopii).\
Obiekty unique pointer tworzy się w następujący sposób:
```cpp
std::unique_ptr<int> my_ptr{ new int{ 23 } };
```
Ewentualnie można użyć metody szablonowej `make_unique`:
```cpp
auto my_ptr = std::make_unique<int>(23);
```

### Zadania
1. Napisz funkcję `agg(func, array)` mającą dwa argumenty;
    * func - funkcję agregującą
    * tablicę typu int\
    Funkcja ma dokonać agregacji danych z tablicy w sposób zdefiniowany za pomocą funkcji func.\
    Zaimplementuj funkcję `agg` na dwa sposoby - tak aby funkcja `func` była przekazywana jako wskaźnik do funkcji i jako wyrażenie lambda.
2. Zapoznaj się z biblioteką Google Test i wykonaj poniższe ćwiczenia:
    * stwórz klasę Vector reprezentującą wektor dowolnego rozmiaru
    * dodaj metodę operator+(const Vector& other) pozwalającą na dodawanie wektorów
    * napisz test jednostkowy z użyciem Google Test sprawdzający poprawność dodawania
3. Skorzystaj z obiektu std::unique_ptr do przechowywania obiektu Vector z powyższego ćwiczenia. Udowodnij że w chwili niszczenia obiektu std::unique_ptr wywoływany jest destruktor przechowywanego przez niego obiektu.