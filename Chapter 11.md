# Chapter 11 | Malware Behavior
## Lab 11-1
1. The malware extracts and drops the file `msgina32.dll` —found as `TGAD` in the `.rsrc` section— to disk.
2. Persistence is achieved by abusing the `SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon` registry key, where the value `msgina32.dll` is stored in the key `GinaDLL`. This way, Winlogon will execute said malicious DLL during the login process.
3. The malware steals user credentials by using GINA interception. This is achieved by capturing credentials through an intermediary DLL called `msgina32.dll` (as opposed to the legitimate `msgina.dll`).
4. Stolen credentials are stored in `C:\Windows\System32\msutil32.sys`. These consist of a username, password, domain, and timestamp.
5. By rebooting the machine (which is when GINA is ready to work after logon), logging in, and displaying the contents of the `msutil32.sys` file.

## Lab 11-2
1. The only export of the DLL is labelled `installer`.
2. Nothing shows up when attempting to install the malware via `rundll32.exe`.
3. `Lab11-02.ini` must reside in the the folder `C:\Windows\System32`.
4. The sample gains persistence by writing `spoolvxx32.dll` to the `AppInit_DLLs` key in the `SOFTWARE\Microsoft\Windows NT\CurrentVersion\Windows` registry location. This way the malicious DLL will be run every time a process loads `User32.dll`.
5. The malware leverages **inline hooking**. Particularly, it hooks the `send` function from the `wsock32.dll` library.
6. The hooking code looks for the string `RCPT TO:` (sent by SMTP). If it founds it, it adds an additional destination email address to the email (by using `RCPT TO:` as well).
7. This malware attacks the processes `THEBAT.EXE`, `OUTLOOK.EXE`, and `MSIMN.EXE`. These are all mail clients, so the malware is trying to steal information sent over said channel. The hook is only installed inside any of these processes whenever they are running.
8. The `.ini` file contains the encrypted email address to where the exfiltrated messages are sent.
9. Activity can be dynamically captured by listening with Wireshark and sending an email from one of the above clients.

## Lab 11-3
1. The DLL contains the interesting string `C:\WINDOWS\System32\kernel64x.dll`. It also imports the functions `GetForegroundWindow` and `GetAsyncKeyState`, which suggest it may install a keylogger. The executable contains the following relevant strings:
	- filenames: `C:\WINDOWS\System32\inet_epar32.dll`, `cmd.exe`, `cisvc.exe`, `command.com`
	- file extensions: `.exe`, `.bat`, `.cmd`
	- services: `net start cisvc`
	
	From this, we can infer that the malware runs a service named `cisvc`, executes shell commands, runs a keylogger, installs the attached DLL (`Lab11-03.dll`) at `C:\WINDOWS\System32\inet_epar32.dll`, and logs the recorded data in `C:\WINDOWS\System32\kernel64x.dll`.
2. When the malware is run, the file `Lab11-03.dll` is copied to `C:\WINDOWS\System32\inet_epar32.dll`. The `C:\WINDOWS\System32\cisvc.exe` is then loaded in memory via `CreateFileMappingA` (with `PAGE_READWRITE` privilege) and `MapViewOfFile` (). Finally, the command `net start cisvc` is run to start the loaded service.
3. To persistently install `Lab11-03.dll`, the executable first copies the file to `C:\WINDOWS\System32\inet_epar32.dll`.  Then it trojanizes the `cisvc.exe` file. It does so by injecting 312 bytes of shellcode which load the `C:\WINDOWS\System32\inet_epar32.dll` DLL and call the `zzz69806582` export.
4. The malware infects the `C:\WINDOWS\System32\cisvc.exe` Windows system file (indexing service) to load the malicious `C:\WINDOWS\System32\inet_epar32.dll`.
5. The interesting function is found in the only export: `zzz69806582`.  There, `StartAddress` is called. This function checks if a mutex labelled `MZ` exists; if so, it exits with return code 0; else, it creates said mutex so there's always only one copy of the program running. After creating the mutex, the file `C:\WINDOWS\System32\kernel64x.dll` is opened for write access. From here, an infinite loop runs a keylogger (aware of shift keys and the running program) implemented via polling through functions `GetForegroundWindow` and `GetAsyncKeyState`. If no keystroke is detected, the malware sleeps for 10 milliseconds, else it records the keystrokes.
6. The malware store the collected data (keystrokes and the pertaining window) in `C:\WINDOWS\System32\kernel64x.dll`.