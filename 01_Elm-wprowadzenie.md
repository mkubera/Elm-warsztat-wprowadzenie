# Czym jest Elm?

Elm to jezyk stworzony do pisania aplikacji webowych, ktory kompiluje sie do JavaScript. Stworzony w 2012 roku przez Evana Czaplickiego, jego syntax jest silnie zainspirowany funkcyjnym jezykiem Haskell.

## Alternatywa dla JavaScript

Elm powstal jako alternatywa dla JavaScript. Z zalozenia Elm ma byc pewniejszym jezykiem, przyjemniejszym w pracy z, pozwalajacym programistom osiagac wieksza satysfakcje i produktywnosc.  
  
Jedna z najwiekszych roznic jest fakt, ze Elm kompiluje sie do JavaScript (i Elmowy program nie skompiluje sie jesli sa w nim bledy), natomiast JavaScript jest interpretowany przez przegladarke (i dopiero na tym poziomie mozna wykryc bledy).  
  
Jesli uzywales/las JavaScript, z pewnoscia `console.log()` bylo czesto uzywana funkcja. Chociaz w Elm istnieje mozliwosc uzycia `console.log()` to nie pamietam kiedy ostatni raz po nia siegalem 😅. Kompilator (i jego pomocne bledy) zastepuja jakiekolwiek potrzeby logowania.

## Static typing

Podczas gdy JavaScript jest jezykiem dynamicznym (dynamic), Elm jest statyczny (static). Roznica jest prosta: statycznosc zmusza nas do precyzowania danych, ktorych uzywamy jako argumentow w funkcjach (input), i danych ktore opuszczaja funkcje (output).

Najlepiej zobrazuje to kilka przykladow:

```elm
-- 0-argumentowa funkcja, ktora zwraca String
name : String
name = "Brenda"

-- 2-argumentowa funkcja, ktora zwraca String (ostatni typ danych, czyli trzeci String)
toFullName : String -> String -> String
toFullName first last =
    first ++ " " ++ last

-- funkcja z modulu String
String.isEmpty : String -> Bool
-- uzycie
String.isEmpty "" -- == True
String.isEmpty "hejka" -- == False
```

Kod okreslajacy typy danych wchodza i wychodze z funkcji (np. `name : String`) nazywamy anotacja (annotation). Ale... Anotacje sa opcjonalne, poniewaz Elm sam wykombinuje o jakie wartosci chodzi w funkcji 😁. Nazywamy to type inference (inferencja typow). W wiekoszsci przykladow na tym warsztacie spotkasz sie z funkcjami bez anotacji.


## Easy refactoring

Refactoring (refaktoryzacja), czyli dokonywanie zmian w aplikacji w Elm przechodzi bezbolesnie i bez stresu! Wszystko dzieki temu, ze kompilator bedzie wskazywal bledy tak dlugo, az nie naprawimy ich wszystkich.  
  
Prosty przyklad:  
Zalozmy, ze mamy funkcje, ktora ma postarzec smoka (`ageDragonByOne dragon`). W tej chwili funkcja jest "hard-coded", i postarza smoka tylko o jeden rok.

```elm
ageDragonByOne dragon =
    {dragon | age = dragon.age + 1}
```

Teraz, chcemy, zeby funkcja postarzala smoka o podana ilosc lat, a wiec:

```elm
ageDragon dragon years =
    {dragon | age = dragon.age + years}
```

Poniewaz zmienilismy nazwe funkcji oraz jej arity (ilosc argumentow), musimy dokonac zmian w calym programie. Ale nasz program jest juz duzy, ma 30 modulow, i nie pamietam ile razy uzylismy funkcji (myslimy, ze 5 albo wiecej).  
  
Oops!  
  
W wiekszosci jezykow musielibysmy rozpoczac polowanie na funkcje i dokonac zmian, majac nadzieje, ze nie wprowadzilismy dodatkowych bugow do programu. W Elm wszystko, co musimy zrobic to zapisac plik, i pozwolic kompilatorowi pokazac nam wszystkie bledne uzycia funkcji!  
  
Bez stresu i bez niepewnosci, co do zmiany czegos w aplikacji 😍.


## Wszystko jest funkcja

```elm
numberSix = 6  -- funkcja (choc wyglada jak zmienna :))

thePrisoner no =
    "I am not a number! " ++ (String.fromInt no)  -- rowniez funkcja

thePrisoner : Int -> String
thePrisoner no =
    "I am not a number! " ++ (String.fromInt no)  -- ta sama funkcja, ale z anotacja (annotation)
```

## Przyjazny kompilator

Elm ma prawdopodobnie najbardziej na swiecie przyjazny programistom kompilator, ktory stara sie jak najbardziej pomoc w odszukaniu bledu. Dzieki temu, ze Elmowy program nie skompiluje sie jesli wykryje bledy przy kompilacji, tzw. runtime errors praktycznie nie istnieja w programach napisanych w Elm!

W trakcie tego warsztatu spotkacie sie wielokrotnie z pomocnymi wiadomosciami generowanymi przez kompilator. Bardzo polecam nastawienie na to, ze bledy w Elm sa czyms dobrym, bo kompilator w wiekoszosci przypadkow pomoze nie tylko znalezc blad, ale jeszcze wskaze potencjalne rozwiazanie 😄


# Koncepty funkcyjnego programowania

## Pure functions ("czyste" funkcje)

Spelnione musza byc dwa warunki, zebysmy mogli otrzymac funkcje typu pure ("czysta"; w odroznieniu od impure).
1) Niezaleznie od tego, ile razy wywolamy funkcje, zawsze otrzymamy ten sam rezultat (output) przy tych samych
argumentach (input).
2) Funkcja nie stworzy zadnych efektow ubocznych (side-effects), np. nie wywola HTTP request, ktory bedzie
mial wplyw na inny system (serwer, baze danych, etc.).

W Elm napisanie impure function ("nieczystej" funkcji) nie jest mozliwe (wobec tego kazda funkcja spelnia warunek #1).
W Elm side-effects sa oddelegowane do komend (Commands / Cmds), ktorych rezultat to albo error, albo rezultat
pozytywny dalej przekazany do jakiejs pure function. (Spelniony warunek #2.)

Przyklad:

```elm
-- funkcja otrzymuje jakis numer jako argument (n) i dodaje do niego 1
addOne n =
    n + 1

-- wielokrotne wywolanie funkcji z tym samym argumentem (input) produkuje ten sam rezultat (output)
addOne 2 -- 3
addOne 2 -- 3
addOne 2 -- 3
```

Z wyjatkiem komend (Cmds), wszystkie funkcje w Elm zyja sobie w bezpiecznym, pewnym swiecie Elm, w ktorym
chaos nie istnieje. Dopiero poprzez Cmds program dokonuje interakcji ze swiatem zewnetrznym, ktory pelen
jest niepewnosci (np. network error, resource, ktorego nie ma pod danym adresem url, bledny url, itd.).

Przyklad Cmd:

```elm
-- wysylamy HTTP GET request do jakiegos url
Http.get
    { url = "http:--url.do/ksiazki" 
    , expect = Http.expectString GotBookText  -- oczekujemy, ze pod danym adresem jest plik .txt z ksiazka
    }
```

Kiedy odpowiedz wraca, potraktujemy ja w zaleznosci od rezultatu (tzn. tego, co wrocilo).
Wyrazamy to w formie `Result`, ktory jest albo bledem (`Http.Error`) albo poprawnie zwroconym tekstem ksiazki (`String`)

```elm
GotBookText (Result Http.Error String)
```

Notatka: na tym warsztacie nie bedziemy pisac zadnych `Cmds`, ale ilustruje ich uzycie (i chce, zebyscie byli swiadomi ich istnienia 😙), zeby w pelni wyjasnic, jak wygladaja pure functions w Elm.


## Functions as first-class citizens (higher order functions)

Funkcje w Elm, podobnie jak w kazdym jezyku funkcyjnym, sa funkcjami wyzszgo rzedu (obywatelami pierwszej kategorii ;-)),
tzn. funkcje moga brac inne funkcje jako argumenty (input), oraz zwracac funkcje (output).

```elm
-- przyklad funkcji (List.map fn []), ktora przyjmuje funkcje (addOne n) jako argument 
addOne n = n + 1

List.map addOne [1,2,3]  -- [2,3,4]
```

Alternatywnie, zamiast `addOne` mozemy uzyc anonimowej funkcji, ktora robi dokladnie to samo (tzn. dodaje `1`).

```elm
List.map (\n -> n + 1) [1,2,3]  -- [2,3,4]
```


## Immutability (persistence (of data))

Jedna z najprzyjemniejszych cech funkcyjnego jezyka jest niezmiennosc danych (immutability). Wezmy prosty przyklad:

```elm
-- lista liczb z jednym elementem, czyli 1
numbers = [1]

-- teraz dodamy numer 2 do listy
2 :: numbers  -- [2,1]

-- jak widac, rezultat jest taki jakiego moglismy oczekiwac (tzn. [2,1]), ale czy lista numbers sie zmienila?
-- sprobujmy sprawdzic
numbers  -- [1]

-- nie. Lista numbers pozostala taka sama. [2,1] (rezultat) to w rzeczywistosci zupelnie nowa lista.

-- jesli chcemy zachowac gdzies te nowo stworzona liste, moze zrobic to tak:
numbers2 = 2 :: numbers

-- jesli sprobujemy sprawdzic zawartosc numbers2, to otrzymamy taki rezultat:
numbers2  -- [2,1]
```

Niektore funkcyjne jezyki pozwalaja celowo zmienic zmienna na mutable (do takich jezykow nalezy np. F#), lecz nie Elm.
Elm pozwala nam uzywac jedynie immutable data, co gwarantuje niezmiennosc danych (persistence of data).


## Declarative code

W odroznieniu od jezykow takich jak JavaScript, PHP, czy inne jezyki imperatywne, Elm (podobnie jak inne funkcyjne jezyki) 
jest jezykiem deklaratywnym. Jaka jest roznica pomiedzy imperatywnym a deklaratywnym (imperative vs declarative)?
Najprosciej rzecz ujmujac, imperatywny jezyk zmusza programiste do pisania w stylu JAK (HOW). Natomiast deklaratywny
jezyk pozwala na pisanie w stylu CO (WHAT). A wiec nie "jak mam to zrobic?", ale "co mam zrobic?" (gdyby komputer nas pytal 😉).  
  
Wysmienitym przykladem jest uzywanie funkcji `List.map`, ktora jest deklaratywna alternatywa do petli typu for (for loop), i
wyglada nastepujaco (pierwszy argument to anonimowa funkcja (anonymous function)):

```elm
List.map (\number -> number * number) [1,2,3]  -- [1,4,9]
```

List.map bierze liste liczb (`[1,2,3]`), i aplikuje anonimowa funkcje do kazdej z nich. Dla celow wylacznie edukacyjnych, tu jest kod, ktory dokona tego samego:

```elm
dblNumber number = number * number

[dblNumber 1, dblNumber 2, dblNumber 3]  -- [1,4,9]
```

Nie interesuje nas JAK `List.map` zmienia kazdy element podanej listy. Obchodzi nas jedynie, CO ma zostac zrobione.  
  
Deklaratywny styl pozwala na pisanie mniej kodu, ktory w dodatku jest bardziej przejrzysty.


## Type system

System typow (type system) jest cecha Elm, ktora pozwala tworzyc wlasne typy danych.  
Tu jest prosty przyklad:

```elm
type Gender 
    = Male
    | Female
```

Teraz mozemy stworzyc record, w ktorym plec (gender) bedzie mogla byc tylko meska lub zenska.

```elm
person1 = 
    { id = 1
    , name = "John"
    , gender = Male
    }

person2 = 
    { id = 2
    , name = "Jane"
    , gender = Female
    }
```

Mozemy tez smialo zwiekszyc ilosc mozliwosci dla typu Gender.

```elm
type Gender 
    = Male
    | Female
    | NonBinary
    | PreferNotToSay
```


## Modules

Moduly i funkcje to dwie rzeczy, z ktorych zbudowane beda nasze programy. Moduly to pliki, ktore pozwalaja grupowac funkcje, jednoczesnie pozwalajac okreslic, ktore z nich sa public, a ktore private.  
  
Kazdy plik (modul) w Elm zaczyna sie od nastepujacej deklaracji:

```elm
module NameOfTheModule exposing (..)
```

Nazwy modulow musza zaczynac sie z wielkiej litery.  
`exposing (..)` oznacza, ze wszystkie funkcje w module sa public, a wiec beda mogly zostac importowane przez inne moduly.  
  
Co jesli chcemy, zeby jakas funkcja byla private? Zalozmy, ze mamy modul `Numbers` z dwiema super prostymi funkcjami (`addToMagicNumber` i `magicNumber`).

```elm
module Numbers exposing (..)

addToMagicNumber n =
    n + magicNumber

magicNumber = 4
```

Zalozmy, ze chcemy, zeby jedynie funkcja `addToMagicNumber` byla importowalna (public), a `magicNumber` nie (private). Wystarczy nam zmienic `exposing (..)` na:

```elm
module Numbers exposing (addToMagicNumber)
```

W ten sposob definiujemy, ktore funkcje sa public, i beda mogly zostac importowane przez inne moduly w taki sposob:

```elm
import Numbers exposing (addToMagicNumber)
```


# Typy danych

## String

String pozwala nam napisac jakis ciag znakow, ktory bierzemy w cudzyslow (np. `"hello"`).

```elm
greeting =
    "Hello world!"
```

Strings mozemy laczyc (concatenate) za pomoca funkcji `(++)`.

```elm
greeting name =
    "Hello " ++ name ++ "!"
```

## Int(eger)

Integer to liczba calkowita.

```elm
4
```

Integer poddaje sie typowym dzialaniom matematycznym.

```elm
4 + 4
4 - 4
4 * 4
4 // 4
4 ^ 4
```

Poniewaz `+`, `-`, etc. to w Elm funkcje (`(+)`, `(-)`, etc.), mozemy tez uzywac je w ten sposob:

```elm
(+) 4 4
(-) 4 4
(*) 4 4
(//) 4 4
(^) 4 4
```


## Float

Float to liczba zmiennoprzecinkowa.

```elm
4.5
```

Podobnie jak integer, float poddaje sie typowym dzialaniom matematycznym.

```elm
4.0 + 4.5
4.0 - 4.5
4.0 * 4.5
4.0 / 4.5
4.0 ^ 4.5
```

## Bool(ean)

Boolean to typ danych, ktory ma tylko dwie mozliwe wartosci: `True` (prawdziwa) lub `False` (nieprawdziwa).

```elm
True
False
1 == 1  -- True
"hi" == "hi"  -- True
1 == 2  -- False
"hi" == "hello"  -- False
```

## Record

Record to typ danych, w ktorym trzymamy dane w formie par klucz=wartosc (key=value pairs).

```elm
{ id = 0
, name = "Harry"
, age = 25
, isAdmin = False
, gradeAvg = 4.2
, hobbies = ["functional programming", "movies", "hiking"]
}
```

Bedziemy wykorzystywac records czesto do gromadzenia danych dotyczacych np. uzytkownikow albo przedmiotow w naszej aplikacji.

```elm
users = 
    [ {id=0, name="Harry"}
    , {id=1, name="Barb"}
    , {id=2, name="Jane"}
    ]
```

W jaki sposob mozna dobrac sie (😁) do danych wewnatrz rekordu? Innymi slowy: getter.

```elm
person = 
    { name = "Joe", age = 53 }

person.name  -- "Joe"
person.age  -- 53
```

A w jaki sposob mozemy cos zmienic w rekordzie? Innymi slowy: update lub setter. Sluzy do tego specjalny syntax: `{ record | field = newValue, field2 = newValue, etc. }`

```elm
updatedPerson = 
    { person | name = "Jane", age = 25 }  
    -- jesli chcemy update'owac wiecej niz jedno pole (key), mozemy je oddzielic przecinkami

-- teraz zobaczmy, co jest wewnatrz updatedPerson
updatedPerson.name  -- "Jane"
updatedPerson.age  -- 25
```

Nie tylko, ze nasz Joe zmienil plec, to jeszcze odmlodnial o 28 lat!! 😁


## List

List pozwala nam zgromadzic jakies wartosci. Poniewaz Elm jest jezykiem statycznym, wartosci musza byc tego samego typu (np. List String, List Bool, List Int).

```elm
strings = 
    ["every", "thing", "is", "a", "function", ":)"]
bools = 
    [True, True, False]
ints = 
    [1,2,3]
ints2 = 
    List.range 1 3  -- [1,2,3]
floats = 
    [1.0, 2.1, 3.2, 4.8]

people =
    [ { id = 0
      , name = "Hideto"
      , tool = "guitar"
      , lovesTheirJob = True
      },

      { id = 1
      , name = "Joanna"
      , tool = "keyboard"
      , lovesTheirJob = True
      },
    ]
```

Nie bedziemy dzis modelowac danych w ten sposob, ale warto wiedziec, ze mozna miec liste zlozona z wielu list :-)

```elm
listOfLists = 
    [ [1,2,3,4], [5,6], [7,8,9] ]
```

### Dodawanie do Listy

Dodawanie elementu do listy zawsze stworzy nowa liste.  
  
Notatka: zauwaz, ze dodawanie elementu odbywa sie za pomoca operatora cons (`(::)`), ktory wymaga, zeby dodawany element byl po lewej stronie operatora, a lista po prawej.

```elm
4 :: [3,2,1]  -- [4,3,2,1]
{name="John"} :: [{name="Jane"}, {name="Larry"}]  -- [{name="John"}, {name="Jane"}, {name="Larry"}]
```

Mozemy rowniez laczyc listy (concatenate).

```elm
[1,2,3] ++ [4,5]  -- [1,2,3,4,5]
[{name="Jane"}] ++ [{name="John"}, {name="Larry"}]  -- [{name="Jane"}, {name="John"}, {name="Larry"}]
```

W pierwszym przypadku uzywamy operatora cons (::), ktory pozwala dodac jeden element do poczatku listy.
Zas w drugim, uzywamy operatora (++), ktory pozwala nam polaczyc dwie listy.  
  
Notatka: zauwaz prosze, iz (++) sluzy rowniez do laczenia Strings.

### Usuwanie z Listy

Usuwanie z listy wymaga uzycia funkcji z modulu `List` o nazwie `filter`. Filter pozwala nam 'filtrowac' elementy w liscie, i wybrac jedynie te, o ktore nam chodzi.   
Filtrowanie odbywa sie za pomoca anonimowej funkcji (lub nazwanej), ktora przyjmuje jeden argument (element z listy), a produkuje `Bool`.
Anonimowa funkcja bedzie wywolana dla kazdego elementu listy.

W ponizszym przykladzie uzywamy operatora `(/=)`, a wiec 'nie rowna sie', i 'usuwamy' z listy liczbe `1`.

```elm
List.filter (\number -> number /= 1) [1,2,3]  -- [2,3]
```

Mozemy smialo usunac z listy wiecej niz jeden element.  
  
W przykladzie ponizej wybieramy jedynie liczby, ktore rownaja sie 3 lub sa wieksze (operator `(>=)`).

```elm
List.filter (\number -> number >= 3) [1,2,3,4,5]  -- [3,4,5]
```


### Update'owanie Listy

Czasem musimy zmienic konkretny element (lub elementy) w liscie lub zmienic nawet wszystkie elementy. W funkcyjnych jezykach do transformacji list sluzy nam funkcja `map`.  
  
Przyklad update'u wszystkich elementow listy:

```elm
List.map (\number -> number + number) [1,2,3]  -- [2,4,6]

List.map (\number -> String.fromInt number) [1,2,3]  -- ["1","2","3"]

List.map (\person -> {person | age = person.age + 1}) [{name="Tom", age=22}, {name="Dick", age=33}, {name="Mortimer", age=44}]  -- [{name="Tom", age=23}, {name="Dick", age=34}, {name="Mortimer", age=45}]
```

Przyklad zmiany jedynie task z id `0`, reszte pozostawiajac bez zmian.

```elm
List.map 
    (\task -> 
        if task.id == 0 then
            -- zmien task 0
            {task | done = True}
        else
            -- reszte pozostaw bez zmian
            task
    ) 
    [{id=0, done=False}, {id=1, done=False}, {id=2, done=False}]  
    -- [{id=0, done=True}, {id=1, done=False}, {id=2, done=False}]
```


### Wybieranie z Listy

Wybieranie z listy jest podobne do usuwania z listy. Zasadniczo jest to to samo (tez uzywamy funkcji `List.filter`). Postanowilem rozpisac to na dwa koncepty, bo tak dyktuje mi doswiadczenie w uczeniu nowych Elmistow 😄.

```elm
List.filter (\str -> str == "John") ["Mary", "Yousef", "John", "Mark"]  -- ["John"]
```

Zauwaz prosze, ze wybierajac cos z listy, otrzymujemy nie sam element, ale liste z elementem (lub wieloma elementami).  
  
Co jesli chcemy wyjac ten konkretny element z listy? Musimy stworzyc nowa funkcje, ktora wezmie jako argument liste strings (`List String`), i wyrzuci z siebie sam string (`String`), pod warunkiem, ze lista bedzie miala tylko jeden string. Jesli lista bedzie pusta albo bedzie miala wiecej niz jeden element, funkcja wyrzuci z siebie pusty string (`""`).

```elm
elementFromList : List String -> String
elementFromList list =
    case list of
        [str] -> str  -- kiedy lista ma tylko jeden element
        _ -> ""       -- w kazdym innym przypadku

-- uzycie
elementFromList ["John"]  -- "John"
elementFromList []  -- ""
elementFromList ["John", "Mark"]  -- ""
```

A co jesli lista bedzie lista nie strings, ale np. integers? W takim przypadku kompilator wyrzuci blad informujac nas o tym, ze prawidlowe zastosowanie funkcji jest z uzyciem `List String`, a nie `List Int`.


## Custom type

Custom types pozwalaja nam na tworzenie wlasnych danych!
Powiedzmy, ze chcielibysmy zaczac hodowac smoki, i nasza hodowla powinna ograniczyc sie tylko do dwoch rodzajow smokow: ognistych i lodowych.

```elm
type DragonKind 
    = Fire
    | Ice 
```

Teraz, gdy chcemy stworzyc smoka, moze on wygladac np. tak:

```elm
dragon = 
    { id = 0
    , name = "Pieszczoszek"
    , kind = Fire
    , ageInYears = 0.8
    , health = 100
    , happy = True
    }
```

Pole `kind` naszego smoka moze miec tylko jedna z dwoch wartosci: `Ice` lub `Fire`.

Ciekawostka: implementadcja typu danych `Bool` w Elm wyglada dokladnie tak:

```elm
type Bool
    = True
    | False
```

Warto tez wspomniec, ze custom types moga tez miec type variables (czyli zmienne). Wygladac to moze tak:

```elm
type Msg
    = ChooseTask Int
```

W tym przypadku bedziemy oczekiwac, ze `Int` to id jakiegos task (zadania). Uzycie takiego typu wygladaloby tak:

```elm
button [ onClick (ChooseTask 1) ] [ text "Choose Task 1" ]
```

Jest to zatem guzik, ktory mozemy kliknac, produkujac `Msg` o wartosci `ChooseTask 1` (tzn. zadania, ktorego id rowne jest 1).
Wiecej na temat Msgs (Messages (Wiadomosci)) w sekcji Architektura Elm (The Elm Architecture).


## Type alias

Type alias na poczatku czesto jest mylony z custom type, poniewaz roznica miedzy nimi wydaje sie wizualnie niewielka. W istocie, roznica jest bardzo duza. Custom type to nowy typ danych (np. Gender albo DragonKind), natomiast type alias to jedynie alias dla jakichs danych.
Mozemy czasem uzywac go do sprawienia, ze program jest bardziej czytelny dla czlowieka (human readable).

```elm
type alias DragonId = Int
```

Od teraz, w naszym Elmowym programie, mozemy uzywac `DragonId` zamiast `Int`. Czy cos to zmienia z punktu widzenia tego jak program dziala? Nie. Ale moze to pomoc programiscie/tom w czytaniu kodu.

Type alias zaczyna lsnic, gdy stosujemy go dla aliasowania records.

```elm
type alias Dragon =
    { id : DragonId
    , name : String
    , kind : DragonKind
    , ageInYears : Float
    , health : Int
    , happy : Bool
    }
```

Od teraz, kiedykolwiek chcemy uzyc rekordu opisujacego naszego smoka, nie musimy podawac wszystkich szeciu pol, albowiem mozemy po prostu uzyc aliasu `Dragon`.

```elm
sayDragonName : Dragon -> String
sayDragonName dragon =
    "Hi! The dragon's name is " ++ dragon.name

-- wywolajmy teraz funkcje
sayDragonName "Pieszczoszek"  -- "Hi! The dragon's name is Pieszczoszek"
```

Na zakonczenie, jesli aliasujemy record, to dostajemy maly bonus w postaci konstruktora (constructor). Od teraz mozemy stworzyc smoka podajac jedynie wartosci w kolejnosci pol.

```elm
Dragon 0 "Pieszczoszek" Fire 0.8 100 True
```

Rezultat bedzie taki sam, jakgdybysmy napisali:

```elm
{ id = 0
, name = "Pieszczoszek"
, kind = Fire
, ageInYears = 0.8
, health = 100
, happy = True
}
```


## Funkcje nazwane i funkcje anonimowe

### Nazwane

W Elm "wszystko" jest funkcja. Dlatego tez duzo czesciej bedziemy uzywali nazwanych funkcji, anizeli anonimowych.

Nazwane funkcje maja nastepujacy syntax:
`nazwaFunkcji argument1 argument2 ... =
    kod wewnatrz funkcji (tutaj mozemy uzywac argumentow)` 

Kilka prostych zasad:
1. Nazwa funkcji musi byc w formie camelCase (tzn. `toJestMojaFunkcja`).
2. Argumenty sa opcjonalne, i moze ich byc wiele.
3. Ostatnia linijka wewnatrz funkcji zwraca jakas wartosc (return)

Ogolna zasada funkcyjnego programowania jest to, ze lubimy miec duzo malych funkcji, z ktorych zbudowany bedzie nasz program. Male funkcje sa latwiejsze do zrozumienia i testowania 😃

Przyklad tworzenia i uzywania funkcji, oraz uzywania funkcji z elm/core library.

```elm
-- 0-argumentowa funkcja
emptyUser =
    { id = 0
    , name = "Jack"
    , trades = ["all"]
    }

-- 2-argumentowa funkcja
addTwoNums num1 num2 = 
    num1 + num2

-- uzycie
addTwoNums 13 12  -- 25
```

Uzywanie funkcji z elm/core library wyglada tak samo, z jedna roznica, zazwyczaj musimy byc specifyczni co do uzywanego modulu.

```elm
-- moduly zaczynaja sie z wielkiej litery, i poprzedzaja nazwe funkcji
String.length "Zool"  -- 4
```

To, czy musimy uzyc nazwy modulu zalezy od tego jak importujemy modul.

```elm
import Html
import Html.Attributes exposing (class)

main =
    Html.div []
        [ Html.button [ class "btn btn-primary btn-circle" ] [ "Click me!" ]
        ]
```

W powyzszym przykladzie, funkcje `div` i `button` musza byc uzyte z poprzedzeniem `Html.`. Natomiast funkcja `class` (poniewaz uzylismy `exposing (class)`) bedzie uzywana bez poprzedzenia `Html.Attributes.`.

### Anonimowa

Anonimowe funkcje wygladaja nastepujaco:

```elm
(\number -> number + 1) 4  -- 5
```

W powyzszym przykladzie `(\number)` jest argumentem funkcji, ktory musi byc nastapiony symbolem strzalki (`->`), a nastepnie kodem, ktory moze uzyc argumentu `number` i zwroci jakas wartosc (w naszym przypadku `number + 1`, czyli `4 + 1`).

Anonimowe funkcje sa najczesciej uzywane przy dzialaniach na listach.

```elm
List.map (\number -> number + 1) [1,2,3]  -- [2,3,4]
```

## Conditionals oraz pattern matching

Conditionals w Elm ograniczaja sie do wzoru `if .. then .. else ..`.

Prosty przyklad:
```elm
if 1 == 1 then
    "It works!!"
else
    "Sad panda 😩"
```

Naprawde ciekawa cecha Elm jest tzw. pattern matching, czyli wywolanie jakiegos kodu, gdy konkretna opcja jest prawdziwa. Uzywamy do tego skladni `case .. of`.  
  
Spojrzmy na ten prosty przyklad:

```elm
case 1 == 1 of
    True -> 
        "Prawda!"
    False -> 
        "Nieprawda..."
```

I bardziej zaawansowany przyklad, ktory uzywa `String`:

```elm
greeting = "Hello"

case greeting of
    "Hi" -> 
        "Czesc"
    "Hello" -> 
        "Witaj"
    _ ->
        "Powitanie nierozpoznane"  -- ta opcja bedzie wywolana jesli greeting nie bedzie ani "Hi", ani "Hello"
```

Podobnie wyglada pattern matching z uzyciem custom type.

```elm
type Door
    = Open
    | Closed
    | NoDoor

basementDoor = NoDoor

case basementDoor of
    Open -> 
        "Drzwi od piwnicy sa otwarte."
    Closed -> 
        "Drzwi od piwnicy sa zamkniete."
    NoDoor ->
        "Oops! Ten dom nie ma drzwi do piwnicy."
```

## Let .. in

Elm pozwala nam tworzyc tymczasowe funkcje wewnatrz innych funkcji.  
  
Najlepiej zobrazuje to prosty przyklad 😄

```elm
groupGreetings : List String -> List String
groupGreetings names =
    let
        updatedNames =
            List.map (\name -> "Hello, " ++ name ++ ".") names

        newNames =
            "Ahoy, Captain Nemo!" :: updatedNames
    in
        newNames

-- teraz, wywolajmy funkcje z lista imion
groupGreetings ["Ishmael", "Ned"]  -- ["Ahoy, Captain Nemo!", "Hello, Ishmael.", "Hello, Ned."]
```

W powyzszym przykladzie, funkcje `updatedNames` oraz `newNames` moga byc uzyte jedynie wewnatrz funkcji `groupGreetings`.  
  
`let .. in` mozemy uzywac w kazdej funkcji, nazwanej i anonimowej.


# Elm CLI

## Podstawowe komendy

`elm init` -- tworzy nowa aplikacje Elm, tzn. plik `elm.json` oraz folder `src/`. Dalej powinnismy sami stworzyc plik `src/Main.elm`.  
  
`elm reactor` -- uzywamy go w katalogu z aplikacja Elm. Po uzyciu, powinnismy otworzyc przegladarke, i skierowac ja do adresu `localhost:8000`. Stamtad wybrac `Main.elm`. W ten sposob zobaczymy nasza aplikacje, a po zmianie kodu bedziemy mogli ja odswiezyc (klawisz `f5` lub `ctrl+r`).  
  
`elm install` -- pozwala na zainstalowanie dodatkowych packages, np. `elm install elm/html`. Po zainstalowaniu bedziemy mogli importowac `Html` w naszych modulach (`import Html`).  
  
`elm make` -- komenda kompiluje Elm do JavaScript w postaci pliku .html. Musimy podac plik z modulem Main, zeby kompilacja sie powiodla (np. `elm make src/Main.elm`).  
  

## Pomoc 

Jesli potrzebujesz dodatkowych informacji, smialo skorzystaj z pomocy: `elm --help`.


# Pierwszy program

## Hello World
```elm
module Main exposing (main)

import Html exposing (text)

main =
    text "Hello World!"
```

## Prosty HTML
```elm
module Main exposing (main)

import Html exposing (div, h1, p, text)

main =
    div []
        [ h1 [] [ text "Jestem header 1" ]
        , p [] [ text "A ja to paragraph" ]
        ]
```

## Dodanie in-line CSS
```elm
module Main exposing (main)

import Html exposing (div, h1, p, text)
import Html.Attributes exposing (style)

main =
    div []
        [ h1 [ style "color" "red" ] [ text "Jestem header 1" ]
        , p [ style "background-color" "yellow" ] [ text "A ja to paragraph" ]
        ]
```


# Architektura Elm (The Elm Architecture), czyli Model-View-Update

Do architectury Elm potrzebujemy modulow `Browser` oraz `Html`. Jezel nie sa zainstalowane, to w konsoli/terminalu powinnismy uzyc `elm install elm/browser elm/html`.  

Teraz, w naszym `Main.elm` musimy je zaimportowac, a potem uzyc funkcji `sandbox` z modulu `Browser` do "aktywowania" architectury Elm.

```elm
module Main exposing (..)

import Browser
import Html exposing (..)
import Html.Events exposing (onClick)

main =
    Browser.sandbox {init = initialModel, update = update, view = view}
```

Jak widzicie, `Browser.sandbox` akceptuje tylko jeden argument, mianowicie record z trzema polami (`init`, `update`, oraz `view`).  
  
Najpierw stworzymy funkcje, ktorych potrzebujemy, a pozniej je omowie wyjasniajac, jak dziala TEA (The Elm Architecture).

```elm
-- MODEL

-- najpierw tworzymy alias dla recordu Model
type alias Model =
    { usernames : List String
    , appTitle : String
    , showTitle : Bool
    }
    
-- pozniej go inicjujemy z konkretnymi danymi
initialModel : Model
initialModel =
    { usernames = ["Blake", "Mortimer"]
    , appTitle = "First TEA app!"
    , showTitle = True
    }


-- UPDATE

-- update wymaga custom type, ktory zwyczajowo nazywamy Msg (skrot od Message)
type Msg
    = ToggleShowTitle

-- funkcja update zawsze bierze dwa argumenty (Msg oraz Model), i wyrzuca z siebie Model
-- update pozwala nam na zmiane modelu w zaleznosci od otrzymanej wiadomosci (Msg)
update : Msg -> Model -> Model
update msg model =
    case msg of
        ToggleShowTitle ->
            -- update'ujemy model zmieniajac pole showTitle,
            -- gdzie jego nowa wartosc to odwrocony Bool
            { model | showTitle = not model.showTitle }
    

-- VIEW

-- view bierze model, pozwalajac wyswietlic dane z pomoca modulu Html
view : Model -> Html Msg
view model =
    div [] 
        [ case model.showTitle of
            True ->
                h1 [] [ text model.appTitle ]
            False ->
                div [] []

        , viewUsernames model
        , button [ onClick ToggleShowTitle ] [ text "Toggle Title" ]
        ]


viewUsernames model =
    div []
        [ h2 [] [ text "Usernames" ]
        , div [] (List.map (\username -> p [] [ text username ]) model.usernames)
        ]
```

[Link do dzialajacej appki](https://ellie-app.com/96YkwnMkgHwa1)  
  
Powyzsza appka robi ciut wiecej, anizeli minimum, mianowicie:
* uzywa pattern matching `case .. of` do pokazywania `model.appTitle` w `view`
* uzywa `List.map` do wyswietlenia wszystkich usernames z `model.usernames`
* ma przycisk, ktory pozwala zmienic wartosc `model.appTitle` z `True` na `False`, i odwrotnie  

ale pomyslalem, ze dobrze bedzie pozwolic sie Wam zapoznac z tymi czesto uzywanymi konstruktami w Elm jeszcze zanim przejdziemy do budowy Appki CRUD.  
  
Duze pytanie, ktore pewnie rodzi sie teraz w Waszych glowach to: Jak Dziala TEA? Juz wyjasniam 😁.
  
Zacznijmy od `view`. Ta funkcja sluzy wylacznie do trzech rzeczy:
* wyswietlania Html
* wyswietlania danych z `Model` (dzieki argumentowi `model`)
* uzywania `Msgs`, ktora pozwalaja user'owi wchodzic w interakcje z programem (np. poprzez klikanie (`onClick`) czy wpisywanie czegos w pola w formie (`onInput`))
A zatem, `view` bierze jako argument `Model`, i wynosi z siebie `Html Msg`, a wiec jakis Html z jakimis Msgs.  
  
Co dzieje sie z nimi dalej? Ha! Dalej, Html (wraz z danymi z `Model`) jest wyswietlany user'owi w przegladarce, ktory np. klika na przycisk. Gdy przycisk jest klikniety, odpowiednia `Msg` zostaje zarejestrowana przez funkcje `update`.  

Teraz, `update` bierze dwa argumenty, mianowicie `Msg` oraz `Model`. Jak widac w przykladzie, bedziemy pattern match'owac na `msg`, i gdy konkretna `Msg` zostanie odebrana przez `update`, jakis konkretny kod zostanie uzyty. Ten kod bedzie musial zwrocic `model`, poniewaz output `update` to `Model`.  
  
Co dzieje sie dalej? Jako, ze dane w `Model` ulegly zmianie (np. `appTitle` zmienilo sie z `True` na `False`), nowy (po update'cie) `Model` zostaje przeslany do `view`, a tam ponownie wyswietlony uzytkownikowi.  
  
I to tyle! 😁  
  
Reasumujac. `Model` (dane) trafia do `view`, ktory ma w sobie jakis `Html` z jakimis `Msgs`. Nastepuje interakcja user'a z aplikacja, ktory opdala jakas `Msg`, ktora trafia do `update`, a tam dochodzi do przeksztalcenia `Model`. Nowy `Model` trafia z powrotem do `view`, gdzie nowe dane zostaja wyswietlone user'owi.  
Ta "petla" sie nie konczy 😎.


# Appka CRUD (Create, Read, Update, Delete)

https://ellie-app.com/97qjntnnQ78a1


# Uzyteczne linki

* [Ellie](https://ellie-app.com/new)
* [Glitch](https://glitch.com/search?q=elm&activeFilter=project)
* [Elm](https://elm-lang.org/)
* [Elm Guide](https://guide.elm-lang.org/)
* [Elm-UI](https://package.elm-lang.org/packages/mdgriffith/elm-ui/latest/)
* [Elm-Live](https://www.elm-live.com/)
* [Elm-Format](https://github.com/avh4/elm-format)
* [Awesome Elm](https://github.com/sporto/awesome-elm) - a list of Elm resources
* [Elm Companies](https://github.com/jah2488/elm-companies/blob/master/README.md) - companies using Elm