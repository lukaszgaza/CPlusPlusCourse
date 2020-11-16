### Klasy w C++

#### Wyjątki
Informują one o sytuacji wyjątkowej, np. błędzie w programie.\
W C++:
* jeżeli nie można łątwo obsłużyć błędu (np. wewnątrz konstruktora) rzuca się wyjątek
* jeżeli łatwo można obsłużyć błąd lokalnie lub błąd może wystąpić podczas normalnego działania programu, to używamy wartości zwracanych do informowania o błędzie

Obsługa wyjątków w C++ wygląda następująco:
```cpp
#include <stdexcept>

void fun() {
    throw std::runtime_error{ "Some exception" };
}

int main() {
    try {
        fun();
    } catch (const std::runtime_error& e) {
        printf("exception caught with message: %s\n", e.what());
    }
}
```
Do "rzucania" wyjątków służy słowo kluczowe `throw`. Po słowie throw może być dowolny obiekt throwable, ale dobrą praktyką jest używanie wyjątków z biblioteki standardowej, jak na przykład `std::runtime_error`.\
Do łapanie wyjątków służy blok `try-catch`. W bloku `try` umieszcza się instrukcje mogące rzucać wyjątki, a w bloku `catch` obsługuje się złapane wyjątki. Jeżeli instrukcja w bloku try wywoła wyjątek normalny tok działania programu jest przerywany i natychmiast wywoływany jest blok catch (o ile sygnatura rzuconego wyjątku odpowiada tej, którą blok catch przechwytuje).

Hierarchia wyjątków w C++:
* std::exception
    * std::runtime_error
        * std::system_error
        * std::overflow_error\
        ...
    * std::logic_error
        * std::invalid_argument
        * std::out_of_range\
        ...
    * błędy na poziomie języka
        * std::bad_cast
        * std::bad_alloc\
        ...

Aby łapać wszystkie wyjątki (niezalecane) stosuje się blok `catch(...)`.
Metody które na pewno nie rzucają wyjątków oznacza się przez `noexcept` np.:
```cpp
int return1() noexcept {
    return 1;
}
```

#### Semantyka kopiowania
##### Konstruktor kopiujący
Domyślny konstruktor kopiujący tworzy kopię obiektu przez tworzenie kopii składowych. Może być to być niebezpieczne, szczególnie w przypadku gdy składowymi są wskaźniki (wtedy oryginał i kopia będą wskazywały na tą samą składową). Rozwiązaniem tego problemu jest własna implementacja konstruktorów kopiujących.
```cpp
class Test {
    Test(const Test& test) {
        ...
    }
};

Test t;
Test a_copy { t };
```
Konstruktor kopiujący jest wywoływany przy przekazywaniu parametrów do funkcji przez wartość.

##### Operator przypisania
Jest innym sposobem wykonywania kopii w C++.\
Różnica pomiędzy operatorem przypisania a konstruktorem kopiującym jest taka, że w przypadku operatora przypisania obiekt do którego przypisujemy może posiadać już wartość, więc przed przypisaniem należy ją wyczyścić.
```cpp
class Test {
    Test& operator=(const Test& other) {
        ...
        return *this;
    }
};
```

##### Domyślne kopiowanie
Zazwyczaj domyśle konstruktor kopiujący i operator przypisania nie są poprawne dla naszego przypadku. Jeżeli jednak są poprawne warto to wprost zaznaczyć w kodzie, aby uniknąć nieporozumień:
```cpp
class Test {
    Test(const Test&) = default;
    Test& operator=(const Test&) = default;
};
```
Czasami nasza klasa nie powinna być kopiowania - możemy wtedy zablokować generowanie konstruktora kopiującego i operatora przypisania:
```cpp
class Test {
    Test(const Test&) = delete;
    Test& operator=(const Test&) = delete;
};
```

#### Semantyka przenoszenia
W sytuacji gdy kopiowanie dotyczy dużych ilości danych może być ono czasochłonne i kosztowne. Czasami chodzi nam tylko o "przekazanie własności" obiektu, czyli wykonanie jego kopii i usunięcie oryginału, ale jest to nieefektywne. Rozwiązaniem jest użycie `move`.\
Semantyka przenoszenia polega przeniesieniu obiektu `y` do obiektu `x` w taki sposób, że po przeniesieniu obiekt x jest równoważny poprzedniej wartości obiektu y, a obiekt y jest w stanie `moved-from` i można wykonać na nim tylko dwie operacje: przypisać do niego nową wartość lub usunąć go.

##### Kategorie wartości
* lvalues - dowolna wartość posiadająca nazwę
    * reference do l-wartości są oznaczane pojedynczym znakiem &, np. `Test& test`
* rvalues - wszystko co nie jest lvalue
    * referencje do r-wartości są oznaczane podwójnym znakiem &, np. `Test&& test`
    * l-wartość można zamienić na r-wartość za pomocą metody `std::move` z pliku nagłówkowego `<utility>`
    * przenoszenie (semantyka przenoszenia) jest możliwe tylko dla r-wartości
    
##### Konstruktor przenoszący
Wywołanie konstruktora przenoszącego jest dużo mniej kosztowne niż wywołanie konstruktora kopiującego. W przypadku konstruktora przenoszącego należy pamiętać o "wyzerowaniu" obiektu, z którego przenosimy.\
Konstruktory przenoszące nie powinny rzucać wyjątków i powinny być oznaczone jako `noexcept`.
```cpp
class Test {
    Test(Test&& other) noexcept {
        ...
    }
};
```

##### Przenoszący operator przypisania
Przenoszący operator przypisania jest równoważnikiem operatora przypisania z tym, że zamiast stałej l-wartości przyjmuje r-wartość.
```cpp
class Test {
    Test& operator=(Test&& other) noexcept {
        ...
        return *this;
    };
};
```

#### Metody generowane przez kompilator
Poniższe metody są generowane przez kompilator.\
Zależności pomiędzy nimi (co jest generowane gdy coś innego zadeklaruje użytkownik) są dość skomplikowane, więc zasadą powinno być implementacja wszystkich pozostałych metod gdy zaimplementujemy część z nich (albo oznaczenie ich jako `default`):
* konstruktor bezargumentowy
* destruktor
* konstruktor kopiujący
* operatror przypisania
* konstruktor przenoszący
* przenoszący operator przypisania

#### Interfejsy
Interfejsy to typy które nie posiadają implementacji metod, jedynie ich deklaracje.\
Użycie ich umożliwia definicję "kontraktu" pomiędzy klientem a naszym kodem. Klient wie jakie metody są dla niego dostępne (to te zdefiniowane w interfejsie), a rzeczywista funkcjonalność ukryta jest w klasach implementujących interfejsy. Interfejsy to klasy, których wszystkie funkcje są funkcjami wirtualnymi (o tym dalej).
```cpp
class TestInterface {
    virtual ~TestInterface() = default;
    virtual void foo() = 0;
};

class Test : TestInterface {
    void foo() override {
    }
};
```
Składnia `class Test : TestInterface {}` oznacza że klasa Test implementuje interfejs TestInterface, a klasa TestInterface jest klasą bazową dla klasy Test.\
Klasa dziedzicząca ma dostęp do wszystkich składowych publicznych i chronionych (protected) klasy dziedziczonej.\
Referencje klasy dziedziczącej można traktować jako referencje klasy bazowej.
```cpp
class BaseClass {}
class DerivedClass : BaseClass{}
void foo(BaseClass& base) {}

int main() {
    DerivedClass derived;
    foo(derived);
}
```
Klasa może zezwolić klasom z niej dziedziczącym na nadpisanie (override) jej metod. Takie metody powinny być oznaczone słowem `virtual`. Implementując taką metodę w klasie dziedziczącej należy ją oznaczyć słowem `override`.
```
class BaseTest {
    virtual void foo() const {
        // some implementation
    }  
}
class DerivedClass : BaseClass {
    void foo() const override {
        // another implementation
    }
}
```
Klasa może wymusić implementację jej metod - zamiast ich implementacji przypisując im wartość 0, są to tak zwane pure virtual methods.
```
class BaseTest {
    virtual void foo() const = 0; 
}
class DerivedClass : BaseClass {
    void foo() const override {
        // another implementation
    }
}
```
Nie można tworzyć instancji klas posiadających czyste metody wirtualne.\
Metody wirtualne umożliwiają polimorfizm czasu wykonania - to z której klasy dana metoda zostanie wywołana zależy od typu obiektu, a nie od typu referencji wskazującej na obiekt.
```cpp
class BaseTest {
    virtual foo() const {}  
};
class DerivedTest : BaseTest {
    void foo() const override {}
};

void testMethod(BaseTest& bt) {
    bt.foo();
}
int main() {
    BaseTest b;
    DerivedTest d;
    BaseTest& br = b;
    testMethod(br); // call foo from BaseTest
    BaseTest& dr = d;
    testMethod(dr); // call foo from DerivedTest 
}
```
Klasa posiadająca wirtualne metody powinna posiadać także wirtualny destruktor (aby podczas wywołania destruktora była wywołana jego wersja zależna od typu obiektu, a nie typu wskazującej na niego referencji).

#### Szablony
C++ osiąga polimorfizm czasu kompilacji przy pomocy szablonów.\
Szablon to klasa lub funkcja posiadająca parametry typu.\
Szablony zastępują wielokrotne kopiowanie tego samego kodu w sytuacji, gdy jedyną różnicą jest typ. Zamiast tego kompilator generuje nową instancję szablonu po odnalezieniu nowej kombinacji typów w parametrach szablonu.\
Poniższy kod definuje szablon parametryzowany typem `T`.
```cpp
template<typename T> class Type {
    T& someFun() {}
};
```
Parametr `T` wewnątrz szablonu można rozumieć jak dowolny typ - w czasie tworzenia instancji szablonu jest zastępowany przez rzeczywisty typ.\
Tworzenie instancji szablonów wygląda następująco:
```cpp
Type<specific_type> t;
```
np.
```
Type<int> t;
```
C++ pozwala także na tworzenie szablonów funkcji:
```cpp
template<typename X, typename Y, typename Z>
X foo(Y& arg1, Z& arg2) {
...
}
```
Przykładowo tworzenie instancji szablonu funkcji może wyglądać tak:
```cpp
auto res = foo<int, string, long>(arg1, arg2);
```
W przypadku szablonów funkcji deklaracja typu może być pominięta, jeżeli typ może być wydedukowany przez kompilator.

#### Koncepty (materiał opcjonalny)
Zdarza się że inicjalizacja szablonu kończy się niepowodzeniem w związku z przekazaniem niekompatybilnego typu - przykładowo funkcja szablonowa wykonuje mnożenie typów parametrycznych, a my jako te typy przekazujemy `char*` który nie wspiera mnożenia. W związku że walidacja poprawności kodu wykonywana jest dość późno w procesie kompilacji (najpier kompilator zastępuje typy parametryczne rzeczywistymi typami, a następnie próbuje kompilować kod) błędy związane z szablonami są trudne do odszyfrowania.\
Z pomocą przychodzą koncelpt (concepts), które nakładają ograniczenia na parametry szablonów. Ograniczenia te pozwalają na wykrywanie błędów typów na poziomie tworzenia instancji szablonów zamiast na poziomie kompilacji co pozwala kompilatorowi na dużo bardziej konkretne (i łatwe do odszyfrowania) informowanie o błędach typów.\
Koncepty pozwalają na wyrażanie wymagań wobec parametrów szablonów na poziomie języka.


### Zadania
1. Stwórz klasę Test posiadającą konstruktor bezargumentowy informujący o swoim wywołaniu, konstruktor kopiujący informujący o swoim wywołaniu oraz destruktor informujący o swoim wywołaniu.
    * stwórz obiekt klasy Test w metodzie main
    * stwórz trzy różne metody test przyjumuące  obiekt klasy Test jako argument przez wartość, wskaźnik i reterencę
    * wywołaj metodę test z metody main \
    Ile razy zostały wowołane konstrutory i destruktory klasy Test w każdym przypadku i dlaczego?

1. Dokończ poniższy program
    ```cpp
    #include <cstdio>

    struct Animal {
        virtual void move() const {
            printf("Animal::move\n");
        }
    };

    struct Dog : Animal {
        // override move here
    };

    struct Cat : Animal {
        // override move here
    };
    
    void move_animal(Animal& animal) {
        animal.move();
    }

    int main() {
        Animal a;
        Dog d;
        Cat c;
        Animal& a1 = a;
        Animal& a2 = d;
        Animal& a3 = c;
        move_animal(a1);
        move_animal(a2);
        move_animal(a3);
    }
    ```
      Co zostanie wypisane na ekranie i dlaczego?

1. Za pomocą szablonów klas stwórz klasę Pair<K1,K2> mogącą przechowywać pary elementów (każdy z elementów może być dowolnego typu).
    * klasa ma mieć metody `first()` i `second` zwracające odpowiednio pierwszy i drugi element pracy
    * zademonstruj działanie klasy w metodzie main 