# Gierka Wojfer 67 - Status Projektu

## O grze
Platformer 2D w pixel-art stylu, **HTML + Canvas + vanilla JS** (bez frameworków).
Bohater: **Wojfer 67** (łysy z trójkątną klatą, w okularach), inspirowany YouTuberem.
**2 poziomy**: salon (wanna boss) → wojferownia (kanapa boss).

## Stack
- 1 plik: `index.html` (HTML+CSS+JS w jednym, ~1820 linii)
- Folder `img/` z ~35 sprite'ami PNG (przezroczyste tło)
- Hostowane na GitHub Pages: https://backloghero-lang.github.io/Game-nr-1-/
- Repo: backloghero-lang/Game-nr-1-

## Stan na 2026-05-03

### Ekran startowy (menu)
- Pokazuje się na starcie gry, po Game Over (zero żyć), po naciśnięciu R
- Tytuł: "WOJFEROWNIA / LEVEL 87" (nazwa kanału YT, stała)
- Wojfer po lewej stronie w pętli wyciskania kanapy (klatki lift_low → mid → high → strain → ... w cyklu 96 ticków)
  - Cały sprite przesuwa się w pionie z fazą lp: `liftY = 70 + (1 - lp) * 35` żeby kanapa schodziła razem z Wojferem (nie rozciągała się)
- Po prawej: opcje START i LEVEL: NORMAL/HARD
- Wskaźnik: mała sztanga (gryf 70px + 2 talerze: czarny + czerwony) lata góra-dół po prawej stronie wybranej opcji
- NORMAL = 3 życia (kolor żółty `#ffd84d`), HARD = 1 życie (czerwony `#ff3333`)
- Sterowanie: ↑↓ + Enter/Spacja (klawiatura), klik/dotyk na canvas (mobile)
- W kodzie: `currentScreen = "menu" | "game"`, `menuSelected = 0|1`, `difficulty = "normal"|"hard"`, `menuT` (tick anim)

### Mechaniki gameplay
- Ruch (strzałki/touch buttons), podwójny skok, kolizje AABB
- Animacja Wojfera (idle/run/jump/fall) z bobbingiem
- 4 typy wrogów: krab, szczur, beczka, ninja - wszystkie odbijają się od krawędzi przepaści
- Skok na głowę zabija wroga (+100 pkt)
- Kolce z ogniem - dotknięcie = śmierć
- Monety = +50 pkt
- ?-blok: uderzenie od dołu uwalnia drop
  - **Co drugi q-blok wypuszcza tarczę** zamiast serca (`(c % 2 === 0) ? "shield" : "heart"`)
  - **Tarcza** = niebieska łuna wokół Wojfera, 1 użycie. Mobek z boku → mob umiera, tarcza znika (+150 pkt). Stomp na mobie nie zużywa tarczy.
  - **Serce** = +1 życie (max 5)
- Drzwi-meta: zamknięte z kłódką dopóki boss żyje
- **Grzejniki jako broń:** Wojfer zbiera grzejniki, klawisz X/Z lub przycisk ✪ rzuca przed siebie (+150 pkt za zabicie)
- HUD amber z glow: serca, monety, WYNIK, grzejnik x N, etykieta levelu (SALON / WOJFEROWNIA)
- Dźwięki: skok, monety, śmierć, stomp (Web Audio API)
- Mobile: touch buttons (◀ ▶ ▲ ✪ R), responsive canvas

### Boss-fight
- **Boss kanapa (lvl 2)**: HP 3, lift_press_low/mid/high/strain klatki, 1.5s wyciskania
- **Boss wanna (lvl 1)**: HP 5, sprite'y bath_low/mid/high/strain (4 klatki + bath_tongue gdy pokonana, oczy X + język)
  - Po każdym wyciśnięciu wanny **spawn losowego mobka** z lewej lub prawej strony (`boss.spawnSide` przełączane co rundę)
  - Animacja wanny używa lift_* sprite'ów (Wojfer pose) z **clipingiem górnych 38%** żeby ukryć kanapę → wanna rysowana osobno na pozycji `liftY + liftHb * 0.05 - 15 * lp`
  - Twarz wanny zmienia się z HP: low → mid → high → strain w miarę utraty HP

### Poziomy
- **Level 1 = SALON** (`MAP1`, 80 kolumn × 13 rzędów)
  - Tło: obrazek `img/background.png` (1600×480, parallax 0.3) - makata-feniks, lampa, TV, ławka do wyciskania
  - Wanna boss na col 70, drzwi F na col 79 row 9
  - Kolce TYLKO na podłodze (4 doły z 3 spike'ami każdy)
- **Level 2 = WOJFEROWNIA** (`MAP2`, 130 kolumn × 15 rzędów)
  - Tło: proceduralna scena (jasne ściany, niebieskie drzwi, kredens, makaty z lotosem)
  - Kanapa boss na col 117, drzwi F na col 125
- Funkcja `loadLevel(n)` przełącza MAP, worldWidth/Height, projectiles, wywołuje buildLevel
- Drzwi w lvl 1 → `loadLevel(2)`, w lvl 2 → `won = true` (game over)
- `currentLevel === 1` triggeruje wannę (isWanna), tło salonu, etykietę "SALON" w HUD

### Sprite'y w img/
- **Player (4)**: `player_idle/run/jump/fall.png` - WYCIETE Z `postawy.png.png` przez user-a (asset pack pixel-art z generatora). Wymiary: idle 113×168, run 117×177, jump 128×149, fall 129×180. Tło przezroczyste (próg jasności > 18 odcina ciemne tło asset packu, zachowuje ciemne spodnie).
- **Lift kanapa (4)**: `lift_press_low/mid/high.png`, `lift_strain.png` - Wojfer + kanapa w jednym sprite, używane do animacji wyciskania w lvl 2 + na ekranie startowym
- **Wanna (5)**: `bath_low/mid/high/strain/tongue.png` - 200×150px, narysowane proceduralnie (PIL): biała emaliowana wanna z brązowymi nogami lwimi, twarz na boku, mimika rośnie z fazą wysiłku
- **Wrogi (4)**: `crab.png`, `rat.png`, `ninja.png`, `barrel.png`
- **Items (5)**: `coin.png`, `heart.png`, `spikes.png`, `qblock.png`, `crate.png`
- **Tła i dekoracje**: `door.png`, `window.png`, `lamp.png`, `couch.png`, `coatrack.png`, `boxes2.png`, `boxes3.png`, `pot.png`, `plant.png`, `poster.png`, `brick.png`
- **Tło salonu**: `background.png` (1600×480) - wygenerowane w Claude Design (makata-feniks, TV, ławka treningowa, czerwone zasłony)
- **Asset pack source**: `postawy.png.png` (1536×1024) - całość paczki Wojfer 67 z generatora AI, zawiera też niewykorzystane: tiles, dekoracje, wrogi, mimika twarzy, paleta kolorów

### Czyszczenie sprite'ów
- Kilka razy iterowaliśmy nad usuwaniem czarnych pikseli-artefaktów wokół postaci AI-generated
- Algorytmy próbowane: flood-fill od krawędzi, eliminacja małych komponentów, erozja morfologiczna, analiza histogramu kolumn, bbox skóry
- **Player sprite'y są user-edytowane** w Paint - to ostateczna wersja po kilku iteracjach. Następnie nadpisane wycinaniem z `postawy.png.png`.
- Couch i lift_* mają legalne ciemne kolory (welurowa kanapa) - czyścimy delikatnie tylko izolowane piksele

## Mapy poziomów (skróty)

### MAP1 (Salon, lvl 1)
80 kolumn × 13 rzędów. Player startuje col 4, wanna boss Z col 70, drzwi F col 79.
Kolce S tylko w 4 dołach na podłodze.

### MAP2 (Wojferownia, lvl 2)
130 kolumn × 15 rzędów. Player col 4, kanapa boss Z col 117, drzwi F col 125.
9 dziur w ziemi do przeskakiwania, 7 grzejników do zebrania, kolce na 3 platformach.

Symbole MAP: `#` ziemia, `c` skrzynka, `q` ?-blok, `S` kolce, `C` moneta, `K` krab, `R` szczur, `B` beczka, `N` ninja, `F` drzwi-meta, `P` start, `Z` boss, `r` grzejnik, `x/y` kartony, `p` doniczka, `t` wieszak, `w` okno.

## Workflow uploadu
User ma GitHub Desktop sklonowany repo w `C:\Users\masly\OneDrive\Dokumenty\GitHub\Game-nr-1-`.
**Brak automatycznego pushowania z Cowork** - nie ma GitHub MCP.
Procedura: zmiany w `Gierka/` → ja kopiuję bezpośrednio do `Game-nr-1-` przez bash → user robi Commit & Push w GitHub Desktop.

**WAŻNE**: Edit tool tnie duże pliki (>1500 linii) na OneDrive. **ZAWSZE używaj Pythona (mcp__workspace__bash) z atomowymi zamianami** zamiast Edit dla zmian w `index.html`. Po każdej zmianie sprawdź `wc -l` (powinno być ~1820 linii) i syntax JS przez `node --check`.

## Co dalej (pomysły do iteracji)
1. **Dokończenie wycinania z postawy.png.png** - są tam też nowe wrogi, dekoracje, tiles, HUD elementy do wykorzystania
2. **Wycięcie animacji boss-lift z `wojfer_sheet.png`** (jeśli user wrzuci - 9 klatek lift down/strain/up + 5 mimik twarzy)
3. **Trzeci poziom** z bossem-fotelem w garażu
4. **Power-upy** (większy skok, niezniszczalność, podwójna prędkość)
5. **Animowane sprite'y biegu** (multi-frame zamiast statyczny)
6. **Muzyka tła**
7. **Zapisywanie postępu** (localStorage - aktualnie nie ma)

## Pliki do zignorowania w folderze
- `assets_sheet.png`, `lift_sheet.png` - oryginalne paczki AI (do ewentualnej re-ekstrakcji)
- `img/postawy.png.png` - asset pack source (zostawiony do dalszego wycinania)
- `img/preview_*.png` - debug previews

## Reguły pracy z user-em (Dariusz, Polak)
- Odpowiadaj **krótko i konkretnie**, po polsku
- Generuj **tylko kod** (lub diff), bez długich wyjaśnień
- Każda odpowiedź = jedna zmiana w pliku lub jeden moduł
- Bez powtarzania całego pliku - tylko zmieniony fragment przez Edit
- Kod prosty, czytelny, działający
- User jest "totalnie zielony" w gicie i web dev - tłumacz po ludzku, krok po kroku
- Polish characters: użyj 'ą ę ć ł ń ó ś ź ż' poprawnie ALE kod komentuje BEZ polskich znaków (problemy z kodowaniem)
- Kluczowy plik: `index.html` w `C:\Users\masly\OneDrive\Dokumenty\Claude\Projects\Gierka\`. Repo: `C:\Users\masly\OneDrive\Dokumenty\GitHub\Game-nr-1-\`. Bash mounts: `/sessions/.../mnt/Gierka/`, `/sessions/.../mnt/Game-nr-1-/`

## Najświeższe zmiany (sesja 2026-05-03)
- Zamiana levelów: salon → lvl 1, wojferownia → lvl 2
- Ekran startowy z menu (START / LEVEL: NORMAL/HARD)
- Wojfer animowany na ekranie startowym wyciska kanapę (jak boss-fight)
- Sztanga jako wskaźnik wyboru w menu
- Mechanika tarczy z q-bloku (niebieska łuna, absorpcja jednego uderzenia z boku)
- Wanna boss z 5 HP, spawn mobków po wyciśnięciu, 5 klatek z mimiką
- Boss-tongue (oczy X + język) gdy wanna pokonana
- Player sprite'y wyciągnięte z `postawy.png.png` (asset pack)
- Czyszczenie iteracyjne czarnych pikseli (z błędami i korektami w Paincie przez user-a)
