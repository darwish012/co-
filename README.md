# AES-128 Encryption in 8086 Assembly

An implementation of the Advanced Encryption Standard with a 128-bit key and a 128-bit block, written in 8086 assembly for the emu8086 emulator (DOS interrupts, `org 100h`, COM-style program). It encrypts one 16-byte block per run and prints the result in hexadecimal.

## What is implemented

- The four AES round steps, each written as an assembler macro:
  - `addkey`: XOR of the state with the round key.
  - `subbytes`: byte substitution through a 256-byte S-box table stored in the data segment.
  - `shiftrows`: row rotation of the 4x4 state.
  - `mix_coloumns`: column mixing with the matrix 02 03 01 01 / 01 02 03 01 / 01 01 02 03 / 03 01 01 02. Multiplication by 2 and 3 in GF(2^8) is done with a shift and a conditional XOR with 1Bh (`multiply_two`, `multiply_three`).
- The key schedule (`keysh` macro: rotate word, S-box, round-constant XOR, word-by-word XOR). The round constants 01, 02, 04, 08, 10, 20, 40, 80, 1B, 36 are in the `rcon` table. Each round key is computed on the fly and written back into `key`.
- The round structure: an initial `addkey`, nine full rounds, and a final round without column mixing (10 rounds in total). The rounds are written out one after another rather than as a loop.
- Input and output routines: the plaintext is read with DOS function 0Ah as 32 hexadecimal characters and converted to 16 bytes; the ciphertext is converted back to 32 hexadecimal characters and printed with DOS function 09h after the text `outis:`.

Encryption only: there is no decryption routine.

## Files

| File | Description |
| --- | --- |
| `code` | The complete assembly source (no file extension). |

## How to run

1. Install emu8086 (or another 8086 assembler that supports `MACRO`, `LOCAL`, `PROC` and DOS `INT 21h`).
2. Copy the contents of `code` into a new source file and save it with an `.asm` extension.
3. Compile and emulate it, then run the program.
4. Type the plaintext as 32 hexadecimal characters and press Enter. The input routine converts letters by subtracting 37h, so use upper-case `A`-`F`.
5. The program prints `outis:` followed by the 32-character hexadecimal result.

## Notes

- The key is not entered at run time. It is the 16-byte `key` array in the data segment (initialised to `2B 28 AB 09 / 7E AE F7 CF / 15 D2 15 4F / 16 A6 88 3C`); edit it to use a different key.
- The state, key and output are stored as 4x4 byte matrices in row order, and the input string is loaded into the state in the same order. If you compare against published AES test vectors, check the byte order you are using.

## Author

Abdelrahman Darwish (GitHub: [@darwish012](https://github.com/darwish012))
