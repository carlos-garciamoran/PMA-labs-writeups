# Chapter 1 | Basic Static Techniques
## Lab 1-1
1. Yes. VirusTotal detects both samples as **trojans**.
2. The compilation times are:
	- `Lab01-01.exe` ==> `Sun 19.12.2010 16:16:19`
	- `Lab01-01.dll` ==> `Sun 19.12.2010 16:16:38`
3. ~~The DLL looks to be packed/obfuscated due to its low number of imports (i.e. `CreateProcessA`).~~ The exe file does not look like it has been packed since its imports reveal some of its actual functionality.
4. The .exe file may modify/create some files (e.g. `FindFirstFile`, `FindNextFile`, and `CopyFile`). The DLL may modify processes (e.g. `CreateProcess` and `Sleep`). `ws2_32.dll` is imported which is used for networking tasks.
5. Could look for the string *WARNING_THIS_WILL_DESTROY_YOUR_MACHINE* and for the filename `C:\windows\system32\kerne132.dll`. The filename of the DLL (`Lab01-01.dll`) is also found in the exe's strings.
6. There is an IP hardcoded in the DLL: **127.26.152.13**. 
7. ~~To erase files or to ask for a ransom in exchange of decrypting them.~~ *The DLL is probably a backdoor. The exe is used to install or run the DLL*

## Lab 1-2
1. Yes. The sample is detected as a a trojan as well.
2. Yes. Firstly, the raw size of its sections (`UPX{0-2}`) is much smaller than its virtual size, and most importantly, `UPX` is a well-known packer. Secondly, its low number of imports contains 2 key functions used by unpacking code: `LoadLibrary` and `GetProcAddress`.
3. When unpacked with UPX, the imports `CreateService`, `OpenSCManager`, and `StartServiceCtrlDispatcherA` suggest that the malware is going to set up a service potentially started at boot time. The import `InternetOpen{Url}` points that the service set up by the sample connects to the internet.
4. The URL `http://www.malwareanalysisbook.com` would be useful as a network-based indicator, whereas the string *MalService* could be used for infected hosts —this is most likely the service's name.

## Lab 1-3
1. Yup. It is listed as a trojan and **spyware**.
2. Yes. According to `DIE`, the packer used is `FSG 1.0`. The 3 PE sections do not have a name and their raw size is much smaller than its virtual size. As before, the `LoadLibrary` and `GetProcAddress` imports being the only ones present proofs that this is clearly a packed sample. Tried unpacking with [unpac.me](https://unpac.me) but didn't get anything back.
3. Can't be answered without unpacking the file.
4. Can't be answered without unpacking the file.

## Lab 1.4
1. Again, the sample is mostly labelled as a trojan and as a **downloader**.
2. Sample does not look obfuscated. Its sections are named as the standards (`.text`, `.rdata`, etc.) and its got many interesting imports. `DIE` does not show the sample is packed either.
3. The compiler stamp is set at `Fri Aug 30 15:26:59 2019`.
4. Some interesting imports are `GetWindowsDirectory`, `{Create,Write}File`, `LoadResource`, and `OpenProcess`. They suggest that the executable writes files to the disk and manages other processes, potentially —maliciously— injecting code into them. *The imports from* `advapi32.dll` *indicate that the program is doing something with permissions.*
5. Host-based indicators include `winlogon.exe` and `\system32\wupdmgr.exe` whereas network-based include `http://www.practicalmalwareanalysis.com/updater.exe`.
6. The resource extracts to a valid PE file. Relevant imports include `URLDownloadToFile` and `WinExec`, which are potentially used to download the executable from the latter URL and runs it. The strings showed in the previous question are also present in this sample.