# x86-64 Assembly: Bracket Matching and Run-Length Encoding

Two string-processing programs written directly in x86-64 assembly (AT&T syntax): one checks whether brackets in a string are balanced, the other compresses repeated characters.

| | |
|---|---|
| **Course** | Computer Organization and Programming (234118), Technion |
| **Semester** | Spring 2025-2026 |
| **Dates** | June 6, 2026 (from the commit history) |
| **Type** | Individual work (all commits are mine) |
| Repo Name | COAP Homework 1 |
| **Language** | x86-64 assembly, GNU assembler (AT&T syntax) |

## My contribution

I wrote both programs, `ex2.asm` and `ex3.asm`, myself. This repository contains only my solutions to exercises 2 and 3. The data they work on is defined separately from these files (see the notes below).

## Skills demonstrated

x86-64 assembly (AT&T syntax), registers and addressing modes, conditional jumps and loop control, byte-level string processing, using bit shifts to emulate a stack, low-level program structure without a runtime.

## What each file does

### `ex2.asm`: balanced brackets

Reads a null-terminated string `exp` made of `()`, `[]` and `{}` and writes `1` to `valid` if every bracket is closed in the right order, or `0` otherwise.

- The "stack" of open brackets lives inside register `%r8`. Pushing a bracket shifts `%r8` left by one byte and stores the bracket in the low byte. Popping shifts it right.
- On a closing bracket, the low byte of `%r8` must be the matching opener, otherwise the string is invalid.
- At the end of the string, the stack must be empty, otherwise some bracket was never closed.
- Because the stack is one 64-bit register, it holds up to 8 nested open brackets.

### `ex3.asm`: run-length encoding

Reads a null-terminated string `str` and writes the result into the buffer `enc`. For every run of identical consecutive characters it writes the run length (as an ASCII digit) followed by the character, then null-terminates the output.

- The run length is stored as a single ASCII character (count + 48), so it is meant for runs of up to 9.
- Characters that are not part of a repeated run are skipped and not copied to the output.
- Example: a run of three `a` characters is written as `3a`.

## Notes on the code

- Both programs are standalone, with entry point `_start`, and use only registers and memory directly: no C library and no function calls.
- `exp` and `valid` (ex2), and `str` and `enc` (ex3), are not defined in these files. They come from a separate data definition that is assembled together with each program.
- I have not included build instructions, because the programs need that data definition to run.

## Files

| File | Purpose |
|---|---|
| `ex2.asm` | Balanced bracket checker for `()`, `[]`, `{}` |
| `ex3.asm` | Run-length encoder for repeated characters |
