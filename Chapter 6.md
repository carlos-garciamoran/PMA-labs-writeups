# Chapter 6 | C Code Constructs
## Lab 6-1
1. An *if* statement.
2. ~~It tries to open and write a file through the `sub_401282` function.~~ It is a call to `printf` which IDA wasn't able to parse correctly.
3. The first test performed is checking `InternetGetConnectedState`'s return code. Interestingly enough, either way `sub_40105F` will be called. The latter, which sets up a buffer, appears to be a wrapper around `sub_401282`. Said function takes as an argument a FILE pointer and 2 integers. *It just freaking prints out if there is an active internet connection or not.* ~~It contains more than 10 local variables and its graph shows great complexity. Among its constructs, several jump tables are displayed which suggest the use of *switch* statements with many cases. *For* loops are also showed in the disassembly. Showing the xrefs from the function, due to the calls to `GetCurrentProcess`, `WriteFile`, `TerminateProcess` and others, it is made clear that this sample attempts to get information from running processes and writes files to disk.~~

## Lab 6-2
1. An *if* statement. Precisely, the one described in the above lab.
2. The subroutine at `40117F` is `printf`.
3. The second subroutine —`sub_401040`— opens the URL `http://www.practicalmalwareanalysis.com/cc.htm` with the user agent `Internet Explorer 7.5/pma` via `InternetOpenUrl`. If it fails, it prints out the corresponding error message; else it reads the first 512 bytes from the response with `InternetReadFile`, followed by more error checking. Lastly, it checks if the first 4 bytes equal to the characters `<!--` which indicate the beginning of an HTML comment. If they don't, it prints an error message.
4. There is a use of nested *if's*. *The subroutine also uses a character array to perform the comment parsing.*
5. Yes. The URL and user agent listed in question #3 are network-based indicators.
6. The malware firstly detects if there is an internet connection. If so, the code will jump to `loc_401148`, which calls `sub_401040` and tries to read data via HTTP from a hardcoded URL. If that succeeds, it will then print the 5th character (right after the comment) from the response and sleep for 1 minute, returning an exit code of 0.

## LAB 6.3
1. The new function called is `sub_401130`. Right after fetching and printing out the parsed command.
2. The parameters taken are [1 byte] character, which corresponds to the command received from the C2, and a filename labelled `lpExistingFileName` which corresponds to `main`'s first argument, `argv[0]`.
3. A *switch* statement which uses a jump table. It consists of 5 cases plus the default.
4. The function's behaviour is determined by its different cases:
	* *case 0* => if it doesn't exist, create the `Temp` directory inside `C:\` via `CreateDirectoryA`
	* *case 1* => copy the contents of the provided filename —`argv[0]`— to `C:\Temp\cc.exe` via `CopyFileA`, and fail if the new filename already exists.
	* *case 2* => delete the file `C:\Temp\cc.exe` via `DeleteFileA`
	* *case 3* => opens the registry key `Software\Microsoft\Windows\CurrentVersion\Run` via `RegOpenKeyExA` to then write `C:\Temp\cc.exe` to value `Malware` via `RegSetValueExA`
	* *case 4* => sleep for 100 seconds via... well, `Sleep` ¯\\\_ (ツ)\_/¯
	* *default* => print `Error 3.2 Not a valid command`
5. After checking for an internet connection and retrieving a command from a C2 —as detailed in the labs above—, the action performed is determined by the latter. The command is substracted by `a` to determine its case. Therefore, `a = 0, b = 1`, and so and so forth, as described above. All of this is done with basic error handling as shown by the several strings and print statements.

## LAB 6.4
1. The functions called are the same, the difference is in their address.
2. A *for* loop around the switch construct in LAB 6.3 (`sub_401150`).
3. The function from the previous lab accepted no parameters while this one accepts 1: the loop counter. The new function appends said counter's value to the user agent and prints it before calling `InternetOpenA`.
4. The loop will iterate 1440 times, as indicated by the `cmp` instruction located at `40125A`. Since there is a 1 minute *sleep* after the command execution, if the command is not `e`,  the program will run for 1440 minutes = 24 hours = 1 day. Else, the run time will be 230,400 minutes, or 160 days.
5. Yes. Both the user agent (plus the `%d` format string) and URL listed in LAB 6.2 are network-based indicators.
6. Its purpose is the same as of the previous sample, although its core functionality is wrapped within the mentioned loop.