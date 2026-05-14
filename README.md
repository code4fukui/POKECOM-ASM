# POKECOM-ASM

> 日本語のREADMEはこちらです: [README.ja.md](README.ja.md)

A Z80-compatible pocket computer emulator project.

## Features

- Z80-compatible CPU
- 8KB RAM
- 24-column, 3-line display with 5x7 dot characters

## Assembler

The project includes a built-in assembler for creating machine code programs. Key features:

- Supports basic assembly language syntax (e.g., labels, constants, directives)
- Ability to assemble code and load it into the emulator's memory
- Examples of calling BIOS routines from assembly

## BIOS

The emulator includes a BIOS with various utility functions:

- Registering and clearing breakpoints
- Reading and writing memory
- Displaying text and graphics
- Accessing the serial port

## Usage

1. Enter the monitor mode (e.g., from BASIC mode)
2. Use the `USER` command to allocate memory for your machine code
3. Edit your assembly code in a text editor
4. Assemble the code using the `A` command
5. Run the code using the `G` command

## License

MIT License — see [LICENSE](LICENSE).