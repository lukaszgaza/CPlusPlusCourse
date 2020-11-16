# User-defined types

### Enumeration - typ wyliczeniowy
Może przyjmować wartości ze zbioru pre-definiowanych wartości.
```cpp
enum class Dice {
    One,
    Two,
    Three,
    Four,
    Five,
    Six
};

Dice dice = Dice::Two;

switch(dice) {
    case Dice::One:
        printf("One");
        break;
    case Dice::Two:
        printf("Two");
        break;
    case Dice::Three:
        printf("Three");
        break;
    case Dice::Four:
        printf("Four");
        break;
    case Dice::Five:
        printf("Five");
        break;
    case Dice::Six:
        printf("Six");
        break;
    default:
        printf("Impossible");
}
```


### Union
Kontenery danych gdzie każy element zajmuje ten sam obszar pamięci (czyli w danym momencie może być używant tylko jeden element).

```cpp
union Employee {
    char name[11];
    int id;
};

Employee e;

strcpy(e.name, "John Smith");
cout << e.name << endl;
cout << e.id << endl; // garbage

e.id = 12345;
cout << e.name << endl; // garbage
cout << e.id << endl;
```

### Class - klasa
Łączą dane z funkcjami modyfikującymi te dane (encapsulation).

#### Metody
Funkcje które tworzą połączenie pomiędzy klasą a jej składowymi. Metody mają dostęp do wszystkich składowych klasy.

#### Kontrola dostępy
* dostęp publiczny (`public`) - każdy ma dostęp do składowej publicznej
* dostęp dla klas dziedziczących (`protected`) - omówimy w przyszłości
* dostęp prywatny (`private`) - domyślny dla klas, tylko metody tej samej instancji klasy mają dostęp do składowych prywatnych

```cpp
class Point {
public:
    double getX() {
        ...
    }

    void setX() {
        ...
    }
    ...
private:
    double x;
    double y;
};

Point p;
```

#### Konstruktory
Metoda o nazwie identycznej z nazwą klasy. Służy do inicjalizacji klasy i zapewnienia poprawności jej niezmienników.

```cpp
class Point {
public:
    Point() { // konstruktor bezargumentowy
        x = 0.0;
        y = 0.0;
    }
    Point (double x_in, double y_in) { // konstruktor z argumentami
        x = x_in;
        y = y_in;
    }
};
```

#### Inicjalizacja zmiennych

##### Inicjalizacja zmiennych podstawowych zerem
```cpp
    int x = 0; // x == 0
    int x{}; // x == 0
    int x = {}; // x == 0
    int x; // x == 0 (może)
```

##### Inicjalizacja zmiennych podstawowych dowolną wartością
```cpp
    int x = 12; // x == 12
    int x{12}; // x == 12
    int x = {12}; // x == 12
    int x(12); // x == 12
```

##### Inicjalizacja tablic
```cpp
    int a[]{1, 2, 3}; // tablica o długości 3: 1, 2, 3
    int a[3]{}; // tablica o długości 3: 0, 0, 0
    int a[3][{1,2}; // tablica o długiści 3: 1, 2, 0
    int a[3]; // tablica o długości 3, niezainicjalizowane wartości
```

##### Inicjalizacja klas
```
class Point {
};

Point p1; // wywołany konstruktor bezargumentowy lub gdy takiego nie ma - domyślny
Point p2{};
Point p3{1.1, 2,2};
Point p4 = {1.1, 2,2};
Point p5 = (1.1, 2,2);
Point p6(); // nie zadziała - to deklaracja funkcji zwracającej Point
```

##### Jak inicjalizować?
Używaj inicjalizacji z nawiasami klamrowymi:
* powoduje najmniej problemów
* prawie zawsze działa zgodnie z oczekiwaniami

#### Destruktor
Funkcja czyszcząca, wywoływana przed usunięciem obiektu.
Prawie nigdy nie wywołuje się ich bezpośrednio, robi to za nas kompilator.
Deklaruje się je jako metody o nazwie identycznje z nazwą klasy, poprzedzone znakiem tyldy (`~`)/
```cpp
class Point {
    ~Point() {
        ...
    }
};
```

### Zadania
1. Stwórz typ wyliczeniowy Operation mający wartości Add, Subtract, Multiply, Divide. Użyj go w instrukcji switch.
2. Stwórz klasę Vector, może być inicjalizowana dowolnie. Dodaj do klasy metodą add(Vector v) pozwalająca na dodawania wektorów. Wynik dodawania powinien być wypisywany na ekran.
