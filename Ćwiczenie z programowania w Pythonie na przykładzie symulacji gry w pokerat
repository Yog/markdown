
###LEAD
Jak wiadomo - najlepszą formą nauki jest zabawa. Dlatego propozycja kolejnego ćwiczenia mającego na celu doskonalenie umiejętności programowania w Pythonie polegać będzie na stworzeniu algorytmu podejmującego decyzję podczas gry w pokera. Zachęcamy do stworzenia kilku wersji algorytmu, których skuteczność będzie można następnie skonfrontować poprzez symulację rozgrywki pokerowej.

###Zasady gry
Proponowana wersja gry wzorowana jest na zasadach Poker Texas Hold'em, ale w celu uproszczenia i wydłużenia rozgrywki zostały wprowadzone pewne zmiany:
* brak możliwości zakładów all-in (wejścia za wszystkie posiadane żetony),
* ograniczenie maksymalnej stawki podbicia.

Dzięki powyższym uproszczeniom unikniemy sytuacji, w której rywalizujące algorytmy kończyłyby grę po zaledwie kilku, albo nawet jednym rozdaniu.


Rysunek1. Schemat przeprowadzania rozgrywki

Rozgrywka prowadzona jest przez aplikację Dealer, która pełni rolę stołu pokerowego:
* dokonuje rozdania - losowania kart graczy oraz kart odkrywanych na stole,
* przelicza i przechowuje stan kont poszczególnych graczy,
* zgodnie z kolejnością rozgrywki odpytuje aplikacje graczy po http,
* waliduje odpowiedzi aplikacji graczy,
* przeprowadza kolejne etapy rozgrywki,
* wyznacza zwycięzcę rozgrywki

Z kolei aplikacja Player pełni rolę gracza - na podstawie otrzymanych od aplikacji Dealer danych wejściowych takich jak m.in.: karty gracza “na ręce”, karty na stole, czy stan konta gracza, zwraca w odpowiedzi decyzję w postaci odzywki pokerowej:
* fold - rezygnacja z dalszej gry w danym rozdaniu,
* check - brak dokonania wejścia przy dalszym pozostaniu w grze (odzywka możliwa tylko w przypadku, gdy stawka w danej rundzie licytacji nie została jeszcze podbita)
* call - wyrównanie do kwoty, którą wniósł ostatni największy zakład w celu pozostania w grze
* bet - pierwsze postawienie zakładu w określonej rundzie
* raise - przebicie stawki zmuszające innych zawodników do wyrównania, przebicia bądź spasowania

Decyzja gracza w ostateczności ewaluowana jest do postaci liczby całkowitej z przedziału od 0 (odzywki fold i check) do maksymalnej skonfigurowanej stawki podbicia.  
Zgodnie z zasadami Texas Hold'em - jeszcze przed rozdaniem kart, dwóch pierwszych graczy automatycznie (bez odpytywania aplikacji Playera) wchodzi za minimalną stawkę (small blind) oraz jej dwukrotność (big blind). Wejścia te liczą się jak normalne zakład.  
Kolejność graczy zmienia się co rozdanie - są oni przesuwani cyklicznie o jedną pozycję w prawo. Gracz kończy grę, kiedy jego stan konta uniemożliwi mu wejście za minimalną stawkę - będzie wynosił zero. Możliwe układy kart, ich starszeństwo oraz pozostałe zasady Texas Hold'em według których toczy się rozgrywka można znaleźć w Internecie, np:
* http://pl.wikipedia.org/wiki/Poker - Wikipedia
* http://www.pokertexas.pl/szkola-pokera/podstawy-pokera/poker-holdem-zasady/
* http://pl.pokerstrategy.com/strategy/others/244/1/

###Przygotowanie środowiska
####Python 3.4
Zarówno aplikacja Dealer jak i Player działają z najnowszym, na chwilę obecną, wydaniem Pythona - 3.4. Pakiety tej wersji interpretera mogą nieznajdować się w repozytoriach niektórych dystrybucji Linuxa, ale można go zainstalować kompilując ze źródeł. Szczegółowy opis takiej instalacji, wraz ze wszystkimi paczkami niezbędnymi do dokonania kompilacji jest opisany dokumentacji skryptu budującego aplikację Dealer: https://github.com/radekj/dealer-buildout.
W przypadku systemów Windows i Mac OS X dostępne są instalatory binarne:
* https://www.python.org/downloads/windows/
* https://www.python.org/downloads/mac-osx/

####Instalacja za pomocą buildout
W celu ułatwienia procesu instalacji i uruchomienia aplikacji w odizolowanym środowisku - obie posiadają skrypty budujące działające w oparciu o system zc.buildout. Komendy służące do pobrania (sklonowania repozytorium źródłowego skryptu budującego) i instalacji aplikacji  Dealer zostały przedstawione w Listingu 1.

```
$ git clone git@github.com:radekj/dealer-buildout.git
$ cd dealer-buildout
$ python3 bootstrap.py
$ ./bin/buildout -vN
```
Listing 1. Komendy do pobrania skryptu budującego i instalacji aplikacji Dealer

Instalacja aplikacji Player przebiega analogicznie, z tym, że adres repozytorium do sklonowania to: `git@github.com:radekj/player-buildout.git`

###Aplikacja Dealer
Aplikacja Dealer działa w oparciu o lekki framework pythonowy - Pyramid. Jego użycie pozwala na wykorzystanie serwera http (Waitress), czy systemu template’ów html (Chameleon) dzięki czemu tworząc własną aplikację można skupić się tylko na samej jej logice.
Po dokonaniu instalacji zgodnie z poprzednim rozdziałem (za pomocą skryptu buildout) - w katalogu zbudowanej aplikacji będą znajdowały się podkatalogi:
* bin - zawierający wykonywalne skrypty, służące np. do wystartowania aplikacji (serwera http)
* eggs - zawierający ściągnięte dodatkowe paczki wykorzystywane w projekcie (np. Pyramid)
* src - zawierający kod źródłowy rozwijanych paczek pobranych z repozytorium, m.in. kod aplikacji Dealer

####Konfiguracja
Konfiguracja środowiska uruchomieniowego (serwera http, logowania, itp.) aplikacji dokonywana jest w pliku ini (np. w pliku `./src/dealer/production.ini)` - można w nim zdefiniować np. numer portu na którym odpowiadać będzie aplikacja. Domyślnie jest to numer 6543. Z kolei w pliku `./src/dealer/dealer/config.py` znajduje się konfiguracja ustawień rozgrywki. Jest ona napisana w postaci kodu Pythona. Można zdefiniować zmienne oznaczające:
* START_ACCOUNT - początkowy stan kont graczy
* SMALL_BLIND, BIG_BLIND - stawki obowiązkowego wejścia graczy na początku licytacji
* MAX_BET_LIMIT - maksymalna stawka przebicia
* PLAYERS - słownik zawierający dane aplikacji graczy biorących udział w rozgrywce: nazwę gracza (wyświetlaną na stronie rozgrywki), początkowy stan konta, adres url (w postaci: host:port) pod którym słucha aplikacja danego gracza

####Uruchomienie
Aplikację uruchamia się za pomocą skryptu wygenerowanego podczas budowania podając ścieżkę do pliku konfiguracyjnego:
`$ ./bin/pserve ./src/dealer/production.ini`
Po uruchomieniu aplikacji - pod adresem zdefiniowanym w pliku konfiguracyjnym (domyślnie: http://localhost:6543/) będzie dostępna strona rozgrywki - Rys. 2.

Rysunek 2. Strona rozgrywki wyświetlana w przeglądarce

####Rozgrywka
Dokonywanie przejść pomiędzy kolejnymi krokami rozgrywki - odpytywanie graczy, przejścia pomiędzy etapami i rozdaniami - odbywa się za pomocą klawisza spacji (pojedyńczy krok) lub enter (wszystkie kroki do końca danej rozgrywki).

###Aplikacja Player
Aplikacja Player ze względu na swoją prostotę napisana jest w czystym Pythonie, bez użycia żadnego frameworku. Serwer http realizowany jest za pomocą modułu wchodzącego w skład biblioteki standardowej interpretera Pythona - wsgiref. Działanie aplikacji polega na odczytaniu parametrów przychodzących w żądaniach http z aplikacji Dealer, uruchomienie kodu obliczającego decyzję gracza na podstawie tych parametrów i zwrócenie odpowiedzi w postaci liczby całkowitej.

```
import configparser
import json
import os

from wsgiref.simple_server import make_server

from player.decision import Decision, chellengers_cards

def application(environ, start_response):
	""" Player wsgi application """
	start_response('200 OK', [('content-type', 'application/json')])
	request_body_size = int(environ['CONTENT_LENGTH'])
	request_body = environ['wsgi.input'].read(request_body_size)
	params = json.loads(request_body.decode('utf-8'))
	decision = Decision(params)
    	result = decision.make_decison()
	response = json.dumps(result)
	return [response.encode('utf-8')]


def run_server():
	""" Running wsgi server """
	config = configparser.ConfigParser()
	config_path = os.path.abspath(os.path.join(
    		os.path.dirname(__file__), '..', 'config.ini'))
	config.read(config_path)
	host = config['server:main']['host']
	port = int(config['server:main']['port'])
	print("Serving on http://%s:%s..." % (host, port))
	make_server(host, port, application).serve_forever()
```
Listing 2. Kod modułu `player.play.py`

W listingu 2. przedstawiony został kod odpowiedzialny za przetwarzanie przychodzących żądań http. Funkcja `run_server` uruchamia funkcję `make_server(host, port, application).serve_forever()` z modułu `wsgiref`, która tworzy i uruchamia serwer wsgi odpowiadający na podanym porcie. Wsgi jest specyfikacją interfejsu komunikacji serwera i aplikacji w Pythonie. Zgodnie z tym protokołem działa większość pythonowych frameworków (m.in. Django, Pyramid, czy Flask). Z kolei funkcja `application` definiuje logikę aplikacji - uruchamia kod przetwarzający parametry przychodzącego żądania (metoda `make_decison`) i zwraca odpowiedź.

####Konfiguracja
Konfiguracja uruchomieniowa aplikacji Player znajduje się w pliku config.ini i daje możliwość zdefiniowania portu, na którym słuchać będzie aplikacja oraz hosta (domyślna wartość 0.0.0.0 oznacza, że aplikacja będzie słuchać na wszystkich zdefiniowanych interfejsach sieciowych - a więc będzie można komunikować się z nią z zewnątrz).

####Uruchomienie
Podczas budowania aplikacji za pomocą narzędzia buildout stworzony zostanie skrypt uruchomieniowy ./bin/play, za pomocą którego można dokonać startu aplikacji Player.

####Testy
Przed przystąpieniem do rozgrywki należy uruchomić zestaw testowy, weryfikujący, czy algorytm decyzyjny działa w prawidłowy sposób. Testy należy uruchomić za pomocą skryptu ./bin/test. Jeżeli nie zostaną zwrócone żadne błędy - można przyjąć, że algorytm decyzyjny działa zgodnie z założeniami i aplikacja Player może przystąpić do rozgrywki.

###Przykładowy algorytm
####Pokereval
Stworzenie wydajnego i trafnego sposobu ewaluacji wartości siły kart pokerowych nie jest prostym zagadnieniem. Dlatego podczas budowania aplikacji Player dodatkowo instalowana jest paczka Pokereval, która pozwala na dokonanie ewaluacji siły kart przy 2, 3, 4, 5, 6, i 7 znanych kartach (karty na ręce + karty na stole). Algorytm Pokereval jest napisany w całości w czystym Pythonie i charakteryzuje się prostym i przejrzystym interfejsem programistycznym. Opiera się na kombinatoryce, z której wynika, że w tali złożonej z 52 kart istnieje C(52,5) = 2 598 960 różnych kombinacji 5-kartowych, z czego 7 462 stanowi układy pokerowe o różnej sile. Dokładny opis działania algortytmu opisany jest w dokumentacji paczki Pokereval: https://github.com/aliang/pokerhand-eval. Jego przykładowe użycie przedstawione zostało w listingu 3. 

```
from pokereval.card import Card
from pokereval.hand_evaluator import HandEvaluator

hole = [Card(12, 2), Card(9, 3)]
board = []
score = HandEvaluator.evaluate_hand(hole, board)
print(score)
0.708898944193
```
Listing 3. Przykład użycia algorytmu Pokereval do ewaluacji siły kart przy 2-kartowym układzie (Q♥ i 9♦).

W wyniku użycia algorytmu, przekazując reprezentację kart (2 karty na ręce + od 0 do 5 kart na stole) otrzymujemy siłę układu w postaci liczby zmiennoprzecinkowej w zakresie od 0 do 1. Im wartość będzie bliższa 1, tym mocniejszy układ kart.

###Przykładowy algorytm
W module `player.decision` paczki aplikacji Player znajduje się kod przykładowego algorytmu decyzyjnego. Klasa Decision enkapsułuje metody niezbędne do stworzenia prostego i przejrzystego algorytmu decyzyjnego. Metoda `__init__` (Listing 4.) inicjalizuje obiekt klasy Decision ustawiając zmienne na podstawie danych otrzymanych w żądaniu od aplikacji Dealer:
* `min_bet` - minimalny zakład, który należy wnieść, aby pozostać w grze,
* `can_raise` - informacja, czy gracz może podnieść stawkę, czy tylko wyrównać do aktualnej,
* `hand` - karty gracza,
* `table` - odkryte karty na stole,
* `chance` - maksymalna wartość kart na ręce i stole obliczona za pomocą algorytmu Pokereval,
* `limit` - maksymalna stawka, którą może postawić gracz,
* `account` - aktualny stan konta gracza,
* `active_players` - informacja o graczach pozostałych w grze.

```
def __init__(self, params):
""" Fetching params from request """
self.min_bet = params.get('min')
self.can_raise = params.get('can_raise')
self.hand = params.get('hand')
self.table = params.get('table')
self.chance = eval_cards(self.hand, self.table)
self.limit = params.get('limit')
self.account = params.get('account')
self.active_players = params.get('active_players')
```
Listing 4. Inicjalizacja obiektu klasy Decision

Klasa `Decision` definiuje również omówione wcześniej metody reprezentujące odzywki możliwe do wykonania przez gracza: `_fold`, `_check`, `_call`, `_bet`, `_raise`.

Listing 5. przedstawia przykładowy algorytm wyliczania i zwracania decyzji gracza.

```
 def make_decison(self):
""" The method for counting and returning your decision.
This is the method that is called by the application engine.
Modify the example code given below to win the game!
"""
# not enought money to play
if self.min_bet > self.account:
return self._fold()
if self.min_bet: # may fold, call or raise
if self.chance < 0.3:
return self._fold()
else:
if self.chance > 0.7 and self.can_raise:
bet = random.choice(range(1, self.limit))
return self._raise(bet)
return self._call()
# may check or b
else:
if self.chance < 0.5:
return self._check()
else:
bet = random.choice(range(1, self.limit))
return self._bet(bet)
```
Listing 5. Przykładowy algorytm decyzyjny.

Przykładowy algorytm, opierając się na sile kart ewaluowanej za pomocą Pokereval pomimo swojej prostoty okazuje się dość skuteczny. Twoim zadaniem będzie zmodyfikowanie tego algorytmu tak, aby jego skuteczność była jeszcze większa. Można to uzyskać na wiele sposobów, np. modyfikując ustawienia kryteriów współczynników decyzyjnych, biorąc pod uwagę liczbę graczy pozostałych w grze, czy ustawiając bardziej deterministycznie wysokość podbicia zakładu (w powyższym przykładzie jest to po prostu losowa liczba). Innym sposobem modyfikacji algorytmu może być na przykład poddanie go adaptacji do warunków gry (bardziej agresywna gra wsród pasywniej licytujących innych graczy lub na odwrót). Wszystkie modyfikacje i pomysły są tutaj dozwolone (dopóki bezbłędnie przechodzić będą testy weryfikacyjne!).

###Rozgrywka
W celu porównania kilku zmodyfikowanych algorytmów podczas symulacji na jednej maszynie proponujemy zbudować aplikację Dealer oraz kilka (4-8) aplikacji Player i odpowiednio skonfigurować porty, na których będą słuchały aplikacje graczy. Należy również pamiętać o odpowiedniej konfiguracji graczy w pliku `./src/dealer/dealer/config.py` (słownik PLAYERS). Po uruchomieniu wszystkich instancji aplikacji - w przeglądarce pod domyślnym adresem http://localhost:6543 będzie wyświetlony stół rozgrywki. Kolejne kroki rozgrywki można uruchomić za pomocą klawisza spacji lub enter. Należy pamiętać, że zrestartowanie aplikacji Dealer spowoduje utratę jej stanu i zresetowanie do wartości początkowej. Jednak po każdym rozdaniu stan gry jest zapisywany w pliku `./src/dealer/dealer/saves/save_YYYYMMDDHHMMSS.py`. Aby wystartować grę wczytując jej stan - należy przed uruchomieniem w pliku `./src/dealer/dealer/config.py` odkomentować import odpowiedniego modułu, np.: `from .saves.save_20141109233056 import *`

###Podsumowanie
Mamy nadzieję, że przedstawiona propozycja będzie stanowić zachętę do pobawienia się, a przy okazji poznania lub pogłębienia swoich umiejętności programowania w Pythonie 3. Zachęcamy również do forkowania repozytorium na githubie. Można pokusić się nawet o rozwinięcie gry przekazując dodatkowe informacje decyzyjne, takie jak informacja o odzywkach poszczególnych graczy, które mogłyby mieć wpływ na podjęcie decyzji przez gracza. Zachęcamy do konfrontacji własnych algorytmów z tymi napisanymi przez kolegów i życzymy świetnej zabawy!

###O Autorze
Radosław Jankiewicz  
radoslaw.jankiewicz@gmail.com  
Radek na co dzień zajmuje się programowaniem aplikacji webowych w Pythonie. Jest entuzjastą open source i stara się aktywnie uczestniczyć w społeczności programistów Python. Od wielu lat współpracuje z firmą STXNext.
