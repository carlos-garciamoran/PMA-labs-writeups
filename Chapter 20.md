# Chapter 20 | C++ Analysis
## Lab 20-1
1. The function at `0x401040` does not take any parameters. The object with which its called is passed through `ecx`.
2. The URL used in the call to `URLDownloadToFile` is `www.practicalmalwareanalysis.com/cpp.html`.
3. This program creates an object which has a hardcoded URL as a parameter and downloads it to `C:\tempdownload.exe`.

## Lab 20-2
1. From the program's strings, it appears that `.doc` and `.pdf` files inside `C:\` are exfiltrated to `ftp.practicalmalwareanalysis.com`.
2. The imports `FindNextFileA` and `FtpPutFileA` suggest that this program **exfiltrates files** through FTP.
3. The purpose of the object created at `0x4011D9` is to store a [pointer to a] `.doc` file. It has a virtual function located at `0x401440` which uploads the contents of its file to the hardcoded FTP server.
4. The `call [edx]` instruction at `0x401349` could call the functions at either `0x401440`, `0x401380`, *or `0x401370`*.
5. To fully analyse the malware without connecting to the Internet, an A record in the host's DNS server should be added resolving the above domain name to a local IP. Said local IP should be in use by a host with an active FTP server containing the directories `docs` and `pdfs`.
6. The purpose of this program is to exfiltrate all `.doc` and `.pdf` files inside `C:\` to the server at `ftp.practicalmalwareanalysis.com` in directories `docs` and `pdfs`, respectively.
7. The purpose of implementing a virtual function call is to **make code refactoring flexible**. For example, if a new file extension were to be targeted, a new class corresponding to it would be created, and then the line of code where the object is created would be changed. This way there is no need to change all the (object) calls individually.

## Lab 20-3
1. From the program's strings, it appears that processes are data is sent to a server via HTTP using both GET and POST commands. The headers include Uesr-Agent, Accept, Content-Length, and others. Hardcoded pages include `/srv.html`, `/put.html`, and `get.html`. A config file `config.dat` is also listed, as well as two Base64 alphabets (one custom, the other one standard).
2. The imports `CreateProcessA`, `TerminateProcess`, `CreateFileA`, `WriteFile`, and several belonging to the `ws2_32.dll` library suggest that this sample manipulates processes, files, and contacts a C2.
3. *At `0x4036F0`, there is a function call that takes the string `Config error`, followed a few instructions later by a call to `CxxThrowException`*.
	- The only parameter the function takes is the above string. Nevertheless, the `this` object is passed through `ECX` as per the `thiscall` calling convention.
	- The function does not return anything, although the `this` object is manipulated inside the function and moved by the caller to `EDX`.
	- This function is the probably the method of an exception class given that `CxxThrowException` is called with the same object as the one used in `0x4036F0`. Hence, this function is used to initialise an exception object for handling errors appropriately.
4. What do the six entries in the switch table at `0x4025C8` do?
	- `loc_402531` sleeps for the number milliseconds returned by the C2
	- `loc_40253B` downloads and stores a file from the C2 in the host
	- `loc_402545` uploads the file returned from the C2 from the infected host
	- `loc_40254F` sends system information (username, major version, etc.) to the C2
	- `loc_402559` calls `CreateProcess` with the argument passed from the C2
	- `loc_402561` does nothing (just frees the object from memory) and is called last by all the upper blocks
5. The purpose of this program is to install an HTTP-based backdoor capable of the above actions.