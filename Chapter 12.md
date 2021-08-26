# Chapter 12 | Covert Malware Launching
## Lab 12-1
1. When the DLL `Lab12-01.dll` (which has no exports) is run, it displays a pop-up saying "Press OK to reboot".
2. The process being injected is `explorer.exe`.
3. By killing or restarting the `explorer.exe` process.
4. The malware operates by performing **DLL injection**.

	The first step is to get a handle to a victim process ID. This is done via the `OpenProcess` function, called at `401000`, which targets `explorer.exe`. Afterwards, `VirtualAllocEx` is called with the process handle retrieved. Next, functions `WriteProcessMemory`, `GetModuleHandleA`, and `GetProcAddress` are called to setup the injection. Finally, to trigger the injection, `CreateRemoteThread` is called with the handle to the victim process, the address of `LoadLibraryA`, and a pointer to the malicious DLL name (in the victim process).

## Lab 12-2
1. The purpose of this program is to covertly launch another one embedded in the resource section.
2. The launcher hides program execution by performing **process replacement** of `svchost.exe`. Firstly, the malicious process is created in suspended state via `CreateProcess`. Then, the victim process's memory is replaced with the malicious executable (in this case, stored encrypted in the resource section). Memory of the target created process is unmapped and a call to `VirtualAllocEx` follows to allocate new memory for the malware, by using `WriteProcessMemory` to write the malware sections into the victim process space —done with a loop. Afterwards, the process environment is restored by calling `SetThreadContext`, which sets the entry point to the malicious code. Finally, `ResumeThread` is called to execute the malware (which was created in a suspended state) which has replaced the victim process.
	
	Process replacement is implemented for malware to disguise as a legitimate process without risking to crash the target process (as happens with process injection).
3. The malicious payload is stored in the `.rsrc` section. It's a resource named `LOCALIZATION` of type UNICODE with a size of 24576 bytes.
4. The malicious payload stored as a resource is XOR-encrypted with key 0x41. The decoding and loading is done at function `sub_40132C`.
5. The strings are protected by XOR key 0x41. The decoding is done at `sub_401000`.

## Lab 12-3
1. The purpose of the payload is to install a keylogger.
2. The malicious payload injects itself by using hooks via `SetWindowsHookExA` to record keystrokes.
3. The file that the program creates is `practicalmalwareanalysis.log`, which is located at the directory where the program was executed. It logs the keystrokes recorded and the program which received them.
	
## Lab 12-4
1. The code at `0x401000` takes a process ID as an argument. It then calls `GetModuleBaseNameA` to get the process name and compare it with the string `winlogon.exe`. If they match, EAX is set to 1; else, it is set to 0. Briefly said, the function checks if the process passed is `winlogon.exe`.
2. The process being injected is `winlogon.exe`.
3. In `sub_401174`, `sfc_os.dll` is loaded via `LoadLibraryA`.
4. The fourth argument passed to the `CreateRemoteThread` call is `lpStartAddress`. Here, it corresponds to the address of ordinal 2 of the `sfc_os.dll` library.
5. Before the actual malware is dropped, the launcher moves the `C:\Windows\system32\wupdmgr.exe` file to `%TEMP%\winup.exe`. Then, the main executable loads the resource `#101` of type BIN and stores its content in `C:\Windows\system32\wupdmgr.exe` —thus masquerading itself as the legitimate Windows update manager. Finally, the dropper runs the actual malware via `WinExec`.

	The payload dropped first runs the `%TEMP%\\winup.exe` executable which corresponds to the legitimate `wupdmgr.exe` (previously backed up by the dropper). It then downloads an executable at `www.practicalmalwareanalysis.com/updater.exe` and stores it in `C:\Windows\system32\wupdmgrd.exe`. Lastly, the downloaded executable is run.
6. The purpose of this and the dropped malware is to download an addtional payload stealthily by:
	1. performing process injection in `winlogon.exe` via the dropper
	2. disabling Windows File Protection
	3. masquerading the downloaded malware as `wupdmgr.exe` and `wupdmgrd.exe`