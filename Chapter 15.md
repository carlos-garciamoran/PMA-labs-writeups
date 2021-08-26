# Chapter 15 | Anti-Disassembly
## Lab 15-1
1. A jump instruction with a constant condition is the technique used in this binary. Specifically, it uses a `xor eax, eax` followed by `jz`.
2. The rogue opcode is `E8` (the opcode for a 5-byte `call` instruction).
3. The faked conditional jump is used 5 times.
4.  The code causing the program to print “Good Job!” is `pdq`.

## Lab 15-2
1. The URL requested is `www.practicalmalwareanalysis.com/bamboo.html`.
2. The User-Agent is generated with the computer's name —via `gethostname`— encrypted with ROT-1 (e.g. `User-PC` -> `Vtfs.QD`).
3. The program looks for the string *Bamboo::* in the initial page request.
4. The sample downloads a file from the URL placed between the *Bamboo::* string and another set of double colons. This file is then manually saved to disk as binary and named `Account Summary.xls.exe`. Finally, the downloaded payload is run via `ShellExecuteA`.

## Lab 15-3
1. The malicious code is called by **abusing main's return pointer** and **misusing structured exception handlers**, repectively.
2. The malware downloads a file from and later executes it with `WinExec`.
3. The URL is `www.practicalmalwareanalysis.com/tt.html` which is stored in memory XOR'ed with key `0xFF`.
4. The filename is `poolsrv.exe`. It is also stored in memory XOR'ed with key `0xFF`.