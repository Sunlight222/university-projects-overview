# MatamStory: a Text-Based RPG Simulation in C++ (Matam Homework 4)

A turn-based game simulation in C++. Players with different jobs and characters go through a cyclic list of events (monsters, a potions merchant, a solar eclipse), gain levels and coins, and compete on a leaderboard until someone reaches the top level or everyone is knocked out. The game is driven by two input files: one for the events and one for the players.

| | |
|---|---|
| **Course** | Introduction to Systems Programming (234124), Technion |
| **Semester** | Winter 2024-2025 |
| **Dates** | January 19-29, 2025 (from the commit history) |
| **Type** | Pair assignment |
| **Language** | C++ (inheritance, polymorphism, abstract classes, factories, smart pointers, exceptions, STL containers) |

## Contribution

| Component | Files | Written by |
|---|---|---|
| Event class hierarchy: `Event`, `Encounter` (combat logic, win and lose outcomes, loot, damage), monsters `Snail`, `Slime`, `Balrog`, special events `SolarEclipse`, `PotionsMerchant` | `Events/` | **Lina Nweisry** |
| `Pack` event (a group of monsters), including its size rules and `getDescription` | `Events/Pack.h/.cpp` | **Lina Nweisry** (first version) |
| `EventFactory::createEvent` and reading the events file | `Events/EventFactory.*`, events part of `MatamStory.cpp` | **Lina Nweisry** (first version) |
| `Player`, `Character` (`Responsible`, `RiskTaking`), `Job` (`Warrior`, `Archer`, `Magician`) | `Players/` | **Partner** |
| `PlayerFactory`, `CharacterFactory`, `JobFactory` | `Players/*Factory.*` | **Partner** |
| Game loop: `play`, `playRound`, `playTurn`, `isGameOver`, leaderboard sorting, reading the players file | `MatamStory.cpp` | **Partner** |
| Refactoring: smart pointers, splitting classes into separate files, header documentation, `log.txt` | across the repository | **Partner** |
| Skeleton, `Utilities`, `main.cpp`, `tests/` | `MatamStory.h`, `Utilities.*`, `main.cpp`, `tests/` | Supplied by the course in the initial commit |

Bug fixes and integration (merges, include fixes, event-index handling, pack-size exception) were shared.


## Skills demonstrated

Object-oriented design with inheritance and virtual functions, abstract base classes, the factory pattern, `std::shared_ptr` and `std::unique_ptr`, exception handling for invalid input files, parsing text files, `std::sort` with a custom comparator, working in a pair with Git branches and merges.

## How it works

### Events (`Events/`)
- `Event` is an abstract class with `getDescription()` and `playTheEvent(Player&)`, which returns the outcome message.
- `Encounter` is an event with a combat power, loot and damage. If the player's combat power is higher than the monster's, the player wins: gains a level and the loot. Otherwise the player loses health equal to the monster's damage. After each encounter the monster is updated.
- `Snail` and `Slime` are basic monsters. `Balrog` gets stronger every time it is played (`updateMonster` raises its combat power).
- `Pack` is a group of at least two monsters read from the events file. Its power, loot, damage and size are the sums of its members, and every member is updated after the fight.
- `SolarEclipse` and `PotionsMerchant` are special events that call into the player, so the effect depends on the player's job (eclipse) or character (potions).
- `EventFactory::createEvent` reads the next event name from the file and builds the matching object. An unknown name throws `std::runtime_error("Invalid Event File")`.

### Players (`Players/`)
- A `Player` has a name, level, force, health and maximum health, coins, one `Character` and one `Job`.
- **Jobs:** `Warrior` (close range, combat power is 2 × force + level, sets its own maximum health at the start), `Archer` (far range, starts with extra coins), `Magician` (far range, the solar eclipse increases force instead of decreasing it). Close-range players lose 10 health after winning an encounter.
- **Characters:** `Responsible` buys as many potions as it can afford until its health is full. `RiskTaking` buys a single potion, and only when its health is low.
- `PlayerFactory`, `JobFactory` and `CharacterFactory` build players from the names in the players file.

### Game (`MatamStory.cpp`)
- The constructor reads both files and throws `Invalid Events File` or `Invalid Players File` for bad input (too few events, or a number of players outside the allowed range).
- Each round, every player who is not knocked out plays one turn: the next event in the list is played on that player (the list wraps around), and the turn details and outcome are printed.
- After each round the leaderboard is sorted by level, then coins, then name.
- The game ends when every player is knocked out or someone reaches the highest level, and the winner (or "no winners") is printed.

## Interface

```
MatamStory <events_file_path> <players_file_path>
```

- The events file is a list of event names (`Snail`, `Slime`, `Balrog`, `Pack <size> <events...>`, `PotionsMerchant`, `SolarEclipse`).
- The players file has one player per line: `<name> <job> <character>`.
- Example inputs and expected outputs are in `tests/` (`test1`, `test2`, `test3`, each with `.events`, `.players` and `.expected`).

## Files

| Path | Purpose |
|---|---|
| `main.cpp` | Opens the two input files and starts the game |
| `MatamStory.h/.cpp` | Game state, file reading, rounds, turns and the leaderboard |
| `Events/` | Event classes, monsters, special events and `EventFactory` |
| `Players/` | `Player`, jobs, characters and their factories |
| `Utilities.h/.cpp` | Message printing helpers (course-supplied) |
| `tests/` | Example inputs and expected outputs (course-supplied) |
| `Dry.pdf` | Written answers |
| `log.txt` | Git log export |

## Build and run

Compile all `.cpp` files with the three folders on the include path, for example:

```
g++ -std=c++17 -I. -IEvents -IPlayers *.cpp Events/*.cpp Players/*.cpp -o MatamStory
./MatamStory tests/test1.events tests/test1.players
```
