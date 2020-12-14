#### Klasy użytkowe

##### optional
`optional` to klasa szablonowa mogąca (ale nie musząca) przechowywać jakąś wartość. Podstawowy przypadek użycia tej klasy to zwracanie wartości z funkcji mogącej zakończyć się niepowodzeniem. Zamiast rzucać wyjątek lub zwracać wiele wartości mogących oznaczać sukces bądź porażkę można zwrócić `optional` zawierający wartość w przypadku sukcesu, a `std::nullopt` w przypadku porażki. Klasa optional znajduje się w pliku nagłówkowym `optional`.

##### pair
Klasa `pair` pozwala na przechowywanie pary elementów.
```cpp
#include <utilities>
struct A{};
struct B{};
std::pair<A, B> ab{ A{}, B{} };
ab.first();
ab.second();
```

##### tuple
Klasa `tuple` pozwala tworzyć krotki - niezmiennicze heterogeniczne kolekcje.
```cpp
std::tuple<int, bool, int> t{ 2, true, 4};
std::cout<<std::get<0>(t)<<std::endl;
std::cout<<std::get<bool>(t)<<std::endl;
std::cout<<std::get<2>(t)<<std::endl;
```

#### Klasy kontenerowe
##### Iteratory
Iteratory to interfejs pomiędzy kontenerami a algorytmami. To typy znające wewnętrzną strukturę kontenera i pozwalające na iteracje nad kontenerem w sposób przypominający używanie wskaźników. Iteratory udostępniają następujące operacje:
* pobranie obecnego elementu (`operator*`)
* przejście do kolejnego elementu (`operator++`)
* przypisanie iteratora do innego iteratora (`operator=`)
Dodatkowo kontenery udostępniają metody `begin()` i `end()` zwracające odpowiednio iteratory wskazujący na pierwszy element kolekcji i wskazujący na miejsce za ostatnim elementem w kolekcji.
  
##### Przegląd klas kontenerowych
Kontenery sekwencyjne:
* std::array - tablica o stałym rozmarze (należy jej używać wszędzie tam, gdzie użylibyśmy wbudowanego typu tabliowego z C++)
* std::vector - tablica o dynamicznym rozmiarze zajmująca sekwencyjny obszar pamięci
* std::list - kontener sekwencyjny z szybkimi operacjami dodawania elementów, ale bez szybkiego dostępu do dowolnego elementu kolekcji

Kontenery asocjacyjne - pozwalają na szybkie wyszukiwanie elementów
* std::set - posortowana kolekcja unikalnych elementów
* std::unordered_set - nieposortowana alternatywa dla std::set
* std::map - pary klucz-wartość (klucze są posortowane)

#### Klasa string
Klasa `std::string` służy do przechowywania ciągów znaków składających się ze znaków typu `char`. Jest alternatywą dla tak zwanych `C-style string` czyli ciągów znaków zakończonych symbolem `NULL`.

##### Wyrażenia regularne
Wyrażenia regularne to ciągi znaków definiujące wzorce wyszukiwania. Pozwalają na wyszukiwanie, zamienianie i ekstrakcję sekwencji znaków z ciągów znaków.\
Przykładowo poniższe wyrażenie regularne może służyć do znajdnowania polskich kodów pocztowych w tekście:
```cpp
#include <regex>

std::regex zip_regex{ "(\d{2]-d\{3})" };
```
Ponieważ wyrażenia regularne często zawierają znaki specjalne które w wyrażeniu należy rozumieć dosłownie (np. `\n`) wyrażenia często definiuje się jako tzw. raw string literals z użyciem `R"<ciąg_znaków>"`.\
Poniższy kod demostruje działanie wyrażeń regularnych:
```cpp
    std::regex regex { R"((\w|\s)*(\d{2}-\d{3}))" };
    std::smatch results;
    std::string text_with_zip{ "My zip code is 12-345" };
    const auto matched = std::regex_match(text_with_zip, results, regex);
    printf("%s\n", results[0].str().c_str());
    printf("%s\n", results[1].str().c_str());
    printf("%s\n", results[2].str().c_str());
```

### Zadania
1. Napisz klasę Vector mogącą przechowywać wektor n-elementowy. Napisz metodę operator+(const Vector& other) pozwalającą na dodawanie wektorów. Metoda ta powinna zwracać optional<Vector>. std::nullopt powinno zostać zwrócone, gdy wymiary dodawanych wektorów nie zgadzają się. Napisz Testy z użyciem Google Test potwierdzające poprawność działania kodu.
1. Napisz klasę Matrix reprezentującą macierz dwuwymiarową dowolnego rozmiaru, przechowującą dane dowolnego typu (klasa generyczna). Macierz ma udostępniać dowolny swój element za pomocą metody T get(const std::pair<int, int>& pos) gdzie pos reprezentuje krotkę (numer_wiersza, numer_kolumny). Klasa ma przechowywać swoją wewnętrzną strukturę w std::array.
1. Należy we własnym zakresie zapoznać się z przedstawionymi klasami kontenerowymi i ich metodami.
1. Należy we własnym zakresie zapoznać się z klasą `std::string` i jej metodami.
1. Zaimplementują funkcję sprawdzającą czy przekazany jako argument ciąg znaków jest palindromem. Argument powinien być typu std::string.