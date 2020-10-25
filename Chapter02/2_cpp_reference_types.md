### Typy referencyjne
To typy przechowujące w pamięci adresy obiektów.
Pozwalają na bardziej ewektywne i eleganckie tworzenie kodu.

#### Wskaźniki
Podstawowy mechanizm odwoływania się do adresów obiektów.

```cpp
int* ptr;
```

##### Adresy obiektów
Adres obiektu pobiera się przez poprzedzaniem nazwy obiektu symbolem `&`.
```cpp
int a{};
int* a_ptr = &a;
print("&a: %p\n", a_ptr); // print address of a
```

##### Dereferencja wskaźnika
Służy do pobierania wartości obiektu wskazywanego przez wskaźnik.
Dłuży do tego operator `*`.
```cpp
int a{};
int* a_ptr = &a;
printf("&a: %p\n", a_ptr); // print address of a
printf("a: %d\n", *a_pt); // print value of a
```

##### Składowe obiektu wskazywanego przez wskaźnik
Operator `->` ma dwa cele:
* dokonuje dereferencji obiektu po jego lewej stronie
* uzyskuje dostęp do składowej wskazywanego obiektu

```cpp
Point p{};
Point* p_ptr = &p;
cout << p_ptr->x;
```

##### Wskaźniki i tablice
Tablica zawiera adres i długość następujących po sobie elementów.
Wskaźnik zawiera adres elementu.
Tablicę można sprowadzić do wskaźnika wskazującego na jej pierwszy element.
```cpp
int a[]{1, 2, 3};
int* a_ptr = a; // a_ptr points to 1

cout<<a_ptr[0]<<endl; // prints 1, square brackets work both with arrays and pointers
```
Konwersja w drugą stronę (wskaźnika na tablicę) jest niedopuszczalna.

##### nullptr
Wskaźnik nie wskazujący na obszar pamięci ma wartość `nullptr`.

#### Referencje
Referencje są bezpieczniejsza alternatywą dla wskaźników.
Referencje deklaruje się z użyciem `$`, nie mogą wskazywać na `null` i nie mogą zmieniać obiektów na które wskazują.
```cpp
void printX(Point& p_ref) {
    cout<<p_ref.x<<endl;
}
Point p{};
printX(p);
```

#### Wskaźnik `this`
Wskaźnik `this` wskazuje wewnątrz klasy na bieżący obiekt danej klasy.
`this` nie zawsze jest potrzebny, bo jest domyślny gdy wskazujemy na składowe, ale przydaje się gdy paramtery metody nazywają się tak samo jak składowe klasy.
```cpp
class Point {
public:
    Point(double x, double y) {
        this->x = x;
        this->y = y;
    }
private:
    double x;
    double y;
};
```

#### const
* argumenty `const`
Gwarantują że argument oznaczony jako `const` nie będzie modyfikowany wewnątrz funkcji (nastąpi wówczas błąd kompilacji)
```cpp
void readOnly(const int* a) {
    a[0] = 3; // compilation error
}
```
* metody `const`
Gwarantują że metody oznaczone jako `const` nie zmienią stanu obiektu
```cpp
class Point {
public:
    double getX() const {
        x = 3; // compilation error
    }
private:
    double x;
    double y;
};
```
Zmienne `const` mogą wywoływać na sobie tylko metody `const`.
* składowe `const`
Składowe `const` nie mogą być zmieniane po inicjalizacji
```cpp
class Point {
public:
    void setX(double x) {
        this->x = x; // compilation error
    }
private:
    const double x = 3;
    double y;
};
```

#### Member Initializer List - Lista Inicjalizacji Składowych
To podstawowy mechanizm inicjalizacji składowych klasy.
Pozwala na inicjalizację wartości składowych `const`.
```cpp
class Point {
public:
    Point(double x, double y): x{x}, y{y} {
    }
private:
    const double x;
    const double y;
};
```
Inicjalizacja jest wykonywana przed wykonaniem ciała konstruktora.

#### Inferencja typów/dedukowanie typów
C++ jest językiem silnie typowanym więc kompilator posiada wiele informacji na temat typów.
W momencie gdy inicjalizujemy zmienną lub przypisujemy do niej wartość zwracaną przez funkcję kompilator może wydedukować typ zmiennej z kontekstu.
Słowo kluczowe `auto` mówi kompilatorowi żeby wykonał taką dedukcję.
```cpp
auto i{3}; // int
auto is_true{false}; // bool
auto name{"John"}; // char[5]

const auto* i_ptr = $i; // const int*
```

#### Kompilacja z użyciem g++/clang++
Kompilator g++ nie wspiera jeszcze całkowicie C++20.
Aby wymusić na nim kompilację w standardzie C++20 należy użyć flagi `-std=c++2a`.
Kompilacja z użyciem clang++ jest analogiczna.\\
```cpp
g++ -std=c++2a test.cpp -o test // kompilacja w standardzie c++20, wynikiem jest plik test
```

### Zadania
1. Napisz klasę Matrix reprezentującą macierz o dowolnym rozmiarze.
2. Napisz funkcję `printMatrix`:
* funkcja ma nie znajdować się wewnątrz żadnej klasy
* funkcja ma przyjmować jako swój argument macierz Matrix
* funkcja ma wypisywać na ekran macierz przekazaną jako swój argument