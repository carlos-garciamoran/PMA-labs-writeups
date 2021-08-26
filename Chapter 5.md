# Chapter 5 | IDA Pro
## Lab 5-1
1. The function `DLLMain` is located at address `0x1000D02E` in the `.text` section.
2. The import `gethostbyname` —from the library `WS2_32`— is located at `100163CC` at `.idata`.
3. `gethostbyname` is called 9 times by 5 different functions. Found 18 xrefs, then divided by 2 —due to counting both the call (`p`) and read (`r`) references.
4. Based on the `name` parameter pushed to the stack and the increment of EAX by 13 bytes, the DNS request will try to resolve the `pics.praticalmalwareanalysis.com` subdomain.
5. 23 local variables —local variables ==> **negative offsets** relative to EBP.
6. 1 parameter —parameters ==> **positive offsets** relative to EBP.
7. The string `\cmd.exe /c` is located at `10095B34` inside the `xdoors_d` section.
8. Previously, functions `GetSystemDirectory` and `GetStartupInfo` are called. After pushing to the stack the string, a jump to `100101DC` is performed. In said function, `strcat` is called with a second parameter labelled `CommandLine`, which will eventually be appended to the first string pushed. Afterwards, the newly formed string is stored in memory by calling `memset`. *This is potentially creating a remote shell for the attacker.*
9. `dword_1008E5C4` appears to be written at `10001678` via a `mov` instruction. Said data can be traced back to function `sub_10003695` which calls `GetVersionExA`, fetching OS Version Information.
10. If the string matches `robotwork`, the `sub_100052A2` function will be called in which the registry key values at `SOFTWARE\Microsoft\Windows\CurrentVersion\WorkTime{?s}` value will be queried. Else, there will be a jump to `loc_10010468`.
11. Firstly, the export `PSLIST` fetches information regarding the OS version. Afterwards, it calls `OpenProcess` and `EnumProcessModules`, therefore enumerating the processes running in the system. It performs a similar functionality to the `ps` UNIX tool.
12. The API function `GetSystemDefaultLangID` is called. Therefore the calling function could be renamed to something like `fetchSystemLanguage`.
13. 4 direct calls (`CreateThread`, `strnicmp`, `strncpy`, and `strlen`). Multiple calls within a depth of 2.
14. 30,000 milliseconds, so 30 seconds.
15. ```
	int af = 2;
	int type = 1;
	int protocol = 6;```
16. The `af` —address family— value corresponds to **AF_INET** which uses IPv4; the `type` value corresponds to **SOCK_STREAM**, which uses TCP; the `protocol` corresponds to **IPPROTO_TCP**.
17. Yes, the `in` instruction is used at `100061DB` with the bytes `56 4D 58 68`, which correspond to the magic string `VMXh`. The function containing said instruction is called from the (3) functions `Install{SB,SA,RT}`, confirming the sample uses VMWare detection techniques.
18. From address `1001D988`, the `.data` section contains **80 bytes** of ASCII characters.
19. After running the script the message *xdoor is this backdoor...* is displayed.
20. By doing right clik and clicking "convert to string" or pressing the *A* key.
21. The script iterates over the 80 bytes. It decrypts each by doing an XOR with byte `0x55`, which then writes back to its location by using `PatchByte`.