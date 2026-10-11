# Matrix and Movie Classes in C++ (Matam Homework 2)

A C++ homework with a "dry" part (a small `Pirate` class plus written answers) and a "wet" part (a dynamically allocated integer `Matrix` class with operator overloading, and a `MataMvidia` class that stores a movie as an array of matrix frames).

| | |
|---|---|
| **Course** | Introduction to Systems Programming (234124), Technion |
| **Semester** | Winter 2025-2026 |
| **Dates** | December 13-24, 2025 (from the commit history) |
| **Type** | Pair assignment |
| **Language** | C++ (classes, dynamic memory, Rule of Three, operator overloading, friend functions) |

## Contribution

| Component | Files | Written by |
|---|---|---|
| `Matrix` class: constructors, destructor, copy and assignment, element access, arithmetic operators, rotation, transpose, Frobenius norm, determinant | `wet/Matrix.h`, `wet/Matrix.cpp` | **Lina Nweisry** |
| `MataMvidia` class: constructors, destructor, copy and assignment, `operator[]`, `operator+`, `operator+=`, `operator<<` | `wet/MataMvidia.h`, `wet/MataMvidia.cpp` | **Raaed Ashkar** (GitHub BlondThunder12), with small fixes by me (added the files, added the `Utilities.h` include) |
| `Pirate` class with a `DEVIL_FRUIT` enum, and its demo `main` | `dry/Pirate.h`, `dry/Pirate.cpp`, `dry/main.cpp` | **Lina Nweisry** |
| Written answers | `dry/dry.docx`, `dry/dry.pdf`, `dry/log.txt` | Shared: I edited the `.docx`, my partner added the PDF versions |
| Error helper (`exitWithError`), test runner, expected output | `wet/Utilities.h/.cpp`, `wet/tests.cpp`, `wet/test.expected` | Supplied by the course in the initial upload (unchanged since) |

Bug fixes after the first implementation were shared: I fixed `rotateClockwise` and the determinant logic, and my partner fixed `operator==`, a segfault in `MataMvidia` and the `rotateCounterClockwise` runtime.

## Skills demonstrated

C++ class design, dynamic memory management with `new[]` / `delete[]`, copy constructor and assignment operator (Rule of Three), operator overloading (member and friend), const-correctness, recursion (determinant), error handling through a provided helper, working in a pair with Git branches and merges.

## How it works

### `Matrix` (wet part, my part)
- Stores `rows`, `cols` and a flat `int*` array in row-major order. Constructors: default (empty), and `(rows, cols, value)`. A negative size exits with an "Out of bounds" error.
- Copy constructor, assignment operator (with a self-assignment check) and destructor manage the array.
- `operator()(row, col)` gives checked read and write access (const and non-const versions). An out-of-range index exits with an error.
- `+`, `-`, `*` between matrices, `+=`, `-=`, `*=`, unary `-`, and multiplication by an `int` on both sides (`m * 3` and `3 * m`). A size mismatch exits with an "Unmatched matrices" error.
- `==` and `!=` compare sizes and every element. `operator<<` prints the matrix.
- `rotateClockwise()`, `rotateCounterClockwise()` (three clockwise rotations) and `transpose()` return new matrices.
- `Matrix::CalcFrobeniusNorm` returns the square root of the sum of squares.
- `Matrix::CalcDeterminant` is recursive cofactor expansion along the first row. It handles sizes 0, 1 and 2 directly and exits with an error if the matrix is not square.

### `MataMvidia` (wet part, my partner's part)
- A movie has a name, an author, and a dynamic array of `Matrix` frames with a length.
- Constructors, destructor, copy constructor and assignment copy the whole frame array.
- `operator[]` returns a frame, with a bounds check.
- `movie1 + movie2` returns a new movie with the frames of both. `+=` accepts either another movie or a single `Matrix` frame.
- `operator<<` prints the name, the author, each frame, and an end marker.

### `Pirate` (dry part, my part)
- A `Pirate` has a name, a bounty and a `DEVIL_FRUIT` (an enum with six fruits and `NONE`, which is the default).
- Getters and setters for each field, and `operator<<` prints `name bounty (Devil Fruit: ...)`.
- `dry/main.cpp` builds a two-pirate crew in an array and prints it.

## Interface

```cpp
Matrix a(2, 2, 1), b(2, 2, 3);
Matrix c = a + b * 2;                  // element-wise and scalar arithmetic
Matrix r = c.rotateClockwise().transpose();
int det = Matrix::CalcDeterminant(c);

Matrix frames[2] = {a, b};
MataMvidia movie("My Movie", "Author", frames, 2);
movie += c;                            // append a single frame
std::cout << movie;                    // prints name, author and all frames
```

## Files

| File | Purpose |
|---|---|
| `wet/Matrix.h`, `wet/Matrix.cpp` | Matrix class |
| `wet/MataMvidia.h`, `wet/MataMvidia.cpp` | Movie class built from matrix frames |
| `wet/Utilities.h`, `wet/Utilities.cpp` | Error types and `exitWithError` (course-supplied) |
| `wet/tests.cpp`, `wet/test.expected` | Test runner and expected output (course-supplied) |
| `dry/Pirate.h`, `dry/Pirate.cpp`, `dry/main.cpp` | Pirate class and demo |
| `dry/dry.docx`, `dry/dry.pdf`, `dry/log.txt` | Written answers and a git log export |

## Build and run

```
g++ -std=c++17 wet/tests.cpp wet/Matrix.cpp wet/MataMvidia.cpp wet/Utilities.cpp -o tests
./tests          # compare the output with wet/test.expected

g++ -std=c++17 dry/main.cpp dry/Pirate.cpp -o pirates
./pirates
```
