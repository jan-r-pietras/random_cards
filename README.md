# Random Cards – Gra w karty (Bridż)

Projekt symulacji rozdania kart do gry w bridża. System zawiera klasy do zarządzania kartami, talią, graczami i stołem do gry.

## Zawartość

- **`random_cards.ipynb`** — Jupyter Notebook z pełną implementacją systemu kart, testami jednostkowymi i interaktywnym interfejsem.

## Wymagania

- Python 3.10+
- Jupyter Notebook (opcjonalnie, aby uruchomić plik `.ipynb`)

## Szybki start

### Uruchomienie notebooka

```bash
jupyter notebook random_cards.ipynb
```

Alternatywnie, jeśli korzystasz z VS Code:
1. Otwórz plik `random_cards.ipynb` w VS Code
2. Zainstaluj rozszerzenie Python
3. Kliknij "Run All" lub uruchamiaj komórki pojedynczo

### Interaktywna gra

Komórka 3 zawiera funkcję `run_cli()`:

```python
run_cli()
```

Program zapyta o:
1. Liczbę graczy (2-4)
2. Imiona graczy (lub domyślne nazwy)

Następnie automatycznie rozdaje 13 kart każdemu graczowi i wyświetla ich ręce.

## Struktura klas

### `Karta`
Reprezentuje pojedynczą kartę do gry.

**Atrybuty:**
- `wartość` — Wartość karty (A, K, Q, W, 10-2)
- `kolor` — Kolor karty (♠️, ♥️, ♣️, ♦️)

**Metody:**
- `get_label()` — Zwraca etykietę karty (np. "K♥️")
- `__str__()` — Zwraca etykietę karty

```python
karta = Karta('K', '♥️')
print(karta)  # K♥️
```

### `CardsCollection`
Abstrakcyjna klasa reprezentująca stos kart (lista kart).

**Metody:**
- `w_stosie()` — Zwraca listę etykiet kart w stosie
- `liczba_kart()` — Zwraca liczbę kart
- `get_labels()` — Statyczna metoda zwracająca etykiety z listy kart

### `Talia`
Reprezentuje talię 52 kart (rozszerzenie `CardsCollection`).

**Metody:**
- `shuffle()` — Tasuje talię (Fisher-Yates)
- `tasuj_fisher_yates()` — Alternatywna metoda tasowania
- `get_first_card()` — Pobiera i usuwa pierwszą kartę z talii
- `get_random_card()` — Losuje i usuwa losową kartę

```python
talia = Talia()
talia.shuffle()
karta = talia.get_first_card()  # Pobiera karę
print(talia.liczba_kart())  # 51
```

### `Osoba`
Bazowa klasa reprezentująca osobę.

**Atrybuty:**
- `imię` — Imię osoby

### `Gracz`
Reprezentuje gracza (rozszerzenie `CardsCollection` i `Osoba`).

**Atrybuty:**
- `imię` — Imię gracza
- `pozycja` — Pozycja gracza (N, E, S, W)
- Karty (dziedziczy z `CardsCollection`)

**Metody:**
- `store_card(karta)` — Dodaje kartę do ręki gracza
- `hand()` — Zwraca listę etykiet kart w ręce
- `hand_sorted()` — Zwraca sortowaną listę (domyślnie po kolorach)
- `hand_sorted_by_color()` — Sortuje po kolorach, potem po wartościach
- `hand_sorted_by_value()` — Sortuje po wartościach, potem po kolorach
- `hand_pretty(sort_by='color')` — Zwraca pięknie sformatowany string

```python
gracz = Gracz(imię='Piotr', pozycja='N')
gracz.store_card(Karta('A', '♠️'))
gracz.store_card(Karta('K', '♥️'))

print(gracz.hand_sorted())  # ['A♠️', 'K♥️']
print(gracz.hand_pretty())
# ♠️: A♠️
# ♥️: K♥️
```

### `Players`
Reprezentuje grupę graczy ze wspólną talią (rozszerzenie listy `Gracz`).

**Atrybuty:**
- `deck` — Wspólna talia kart dla wszystkich graczy

**Metody:**
- `deal()` — Rozdaje 13 kart każdemu graczowi

```python
gracze = [Gracz(imię='Piotr', pozycja='N'), Gracz(imię='Maria', pozycja='E')]
stol = Players(gracze, deck=Talia())
stol.deck.shuffle()
stol.deal()  # Każdy gracz ma teraz 13 kart
```

## Przykłady użycia

### Przykład 1: Podstawowa gra

```python
# Utwórz graczy
gracze = [
    Gracz(imię='Anna', pozycja='N'),
    Gracz(imię='Bogdan', pozycja='E'),
    Gracz(imię='Celina', pozycja='S'),
    Gracz(imię='Dariusz', pozycja='W')
]

# Utwórz stół z talią
stol = Players(gracze, deck=Talia())

# Tasuj i rozdaj karty
stol.deck.shuffle()
stol.deal()

# Wyświetl ręce
for gracz in stol:
    print(f"{gracz.imię}: {gracz.hand_sorted()}")
```

### Przykład 2: Różne sposoby wyświetlania

```python
gracz = Gracz(imię='Jan', pozycja='N')

# Dodaj karty
for _ in range(13):
    talia_temp = Talia()
    talia_temp.shuffle()
    gracz.store_card(talia_temp.get_first_card())

# Sortowanie po kolorach
print("Po kolorach (domyślne):")
print(gracz.hand_sorted_by_color())

# Sortowanie po wartościach
print("\nPo wartościach:")
print(gracz.hand_sorted_by_value())

# Piękny format (pogrupowany po kolorach)
print("\nPiękny format:")
print(gracz.hand_pretty())

# Piękny format (pogrupowany po wartościach)
print("\nPiękny format (po wartościach):")
print(gracz.hand_pretty(sort_by='value'))
```

### Przykład 3: Używanie funkcji pomocniczych

```python
# Uso funkcji create_and_deal
gracze = create_and_deal(num_players=4, names=['Piotr', 'Agata', 'Kasia', 'Karol'])

# Wyświetl tabelę
show_table(gracze)
```

## Testy jednostkowe

Komórka 5 zawiera 20 testów jednostkowych dla wszystkich klas:

- **TestKarta** (6 testów) — Inicjalizacja, `get_label()`, reprezentacja
- **TestTalia** (6 testów) — Inicjalizacja, tasowanie, pobieranie kart
- **TestGracz** (5 testów) — Przechowywanie kart, wyświetlanie rąk
- **TestPlayers** (3 testy) — Inicjalizacja, rozdawanie kart

Aby uruchomić testy, wykonaj komórkę 5. Wszystkie testy powinny przejść (20/20 OK).

## Demonstracja funkcji

Komórka 6 zawiera demonstrację wszystkich opcji sortowania i formatowania:

- Testuje różne metody sortowania
- Pokazuje piękny format zagrupowany po kolorach
- Pokazuje piękny format zagrupowany po wartościach
- Wyświetla całą tabelę z przykładowymi graczami

## Pozycje graczy

Standardowe pozycje w bridżu:
- **N** (North/Północ) — Górny gracz
- **E** (East/Wschód) — Prawy gracz
- **S** (South/Południe) — Dolny gracz
- **W** (West/Zachód) — Lewy gracz

## Wartości kart

Karty są oceniane od najwyższej do najniższej:
- **A** (As)
- **K** (Król)
- **Q** (Dama)
- **W** (Walet/Knecht)
- **10**, **9**, **8**, **7**, **6**, **5**, **4**, **3**, **2**

## Kolory kart

- **♠️** — Piki (Spades)
- **♥️** — Kiery (Hearts)
- **♣️** — Trefle (Clubs)
- **♦️** — Kara (Diamonds)

## Notatki dotyczące kodu

1. **Kodowanie Unicode** — Projekt używa emoji dla kolorów kart, zapewne że terminal/IDE obsługuje UTF-8.

2. **Algorytm tasowania** — Talia używa algorytmu Fisher-Yates (`random.shuffle()`), który gwarantuje losowość O(n).

3. **Hierarchia klas** — `Gracz` dziedziczy zarówno po `CardsCollection` (lista kart) jak i `Osoba` (imię), co pozwala graczowi być zarówno kolekcją kart jak i osobą.

4. **Testy** — Wszystkie klasy mają coverage testami jednostkowymi za pomocą `unittest`.

## Autor

Projekt edukacyjny — symulacja gry w karty (bridż) w Pythonie 3.10+.

## Licencja

MIT (lub inne, do określenia przez użytkownika)
