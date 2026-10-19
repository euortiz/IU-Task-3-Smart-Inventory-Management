# Smart Inventory Management System

Command-line inventory application for a small hardware shop, written in C++17.
Portfolio project for **Project: General Programming with C/C++ (DLBMINPAPCC01_E)**, IU Internationale Hochschule – Task 3.

> **Status: Phase 1 (Conception).** The repository contains the build skeleton only. Features are implemented in Phase 2.

## Features (planned)

- Add, edit and remove products
- Record stock movements (restock / sale) that update quantities
- Search products by name and sort by name, quantity or price
- Low-stock report and transaction history
- Data saved to a text file and reloaded on start-up
- Input validation (invalid numbers, empty text, duplicate IDs, negative stock)

## Requirements

- C++17 compiler: GCC 11+ or Clang 14+
- CMake 3.16+
- Git

## Build and run

```bash
git clone <repository-url>
cd <repository-folder>
cmake -S . -B build
cmake --build build
./build/inventory
```

On Windows (MSVC) the executable is created at `build\Debug\inventory.exe`.

## Run the tests

```bash
cmake --build build
ctest --test-dir build --output-on-failure
```

## Project structure

```
.
├── CMakeLists.txt
├── README.md
├── docs/
│   └── codebook.md        # data structures, file format, design decisions
├── src/
│   ├── main.cpp           # entry point
│   ├── ui/Menu.*          # interface layer: console menu, input validation
│   ├── logic/Inventory.*  # logic layer: operations, search, sort, reports
│   └── data/
│       ├── Models.h       # Product and Transaction structs
│       └── Storage.*      # data layer: load/save inventory.dat
└── tests/
    └── test_main.cpp      # assert-based tests, run through CTest
```

## Architecture

Three layers, each one only talking to the layer next to it:

```
User  <->  Interface layer (Menu)  <->  Logic layer (Inventory)  <->  Data layer (Storage)  <->  inventory.dat
```

- **Product**: id, name, category, quantity, unitPrice, minStock, supplier
- **Transaction**: id, productId, delta (+ restock / − sale), timestamp, note
- One product has many transactions, linked by `productId`.

### Memory model

- Records are stored by value in `std::vector`, owned by a single `Inventory` object.
- Lookups return a non-owning `Product*` (or `nullptr`) that is never stored long-term.
- RAII throughout: no manual `new`/`delete`, file streams close automatically, and `Storage` is owned through `std::unique_ptr`.

## File format

The data is kept in `inventory.dat`, a UTF-8 text file with one record per line and fields separated by `|`:

```
#INV v1
P|1|Hammer|Tools|25|9.90|5|Bosch
T|1|1|-3|2026-10-19T10:15|sale
```

| Prefix | Fields |
|---|---|
| `P` | id, name, category, quantity, unitPrice, minStock, supplier |
| `T` | id, productId, delta, timestamp, note |

- The file is loaded on start-up and rewritten after every change. Changes are written to a temporary file that is then renamed, so a crash cannot corrupt the data.
- A missing file starts an empty inventory.
- Malformed lines are skipped and reported with their line number.
- The `|` character is not allowed in text input.

## Roadmap

- [ ] Phase 1 – Conception document, repository and build skeleton
- [ ] Phase 2 – Implementation, tests, code book and presentation
- [ ] Phase 3 – Hardening, 2-page abstract and demo video

## Author

Gustavo Ortiz
