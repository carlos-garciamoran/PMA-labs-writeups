# Chapter 3 | Basic Dynamic Analysis
## Lab 3-1
1. The most interesting strings found are:
	- `vmx32to64.exe`; this is probably the filename of an executable that will get written to disk. Could serve as a host-based indicator.
	- `CONNECT %s:%i HTTP/1.0`; most likely this HTTP command will be used to connect to the C&C server.
	- `www.practicalmalwareanalysis.com`; this URL could be used as a network-based indicator and most likely functions as the C2 server.
	- `SOFTWARE\Classes\http\shell\open\commandV`; this non-standard registry value suggests data is being written to it. This string could also be a good IOC.
	- `SOFTWARE\Microsoft\Windows\CurrentVersion\Run`; this well-known registry value holds the executables which will be run at startup, hence achieving persistence.

	The only import found is `ExitProcess` from `kernel32.dll`. DIE doesn't show any packers, although given that only one function is imported it's probable that the executable is packed.
2. See #1.
3. Idem.

## Lab 3-2
1. To install the DLL we can run the command `rundll32.exe Lab03-02.dll, Install`.
2. To run the sample, the service installed can be started by running `net start IPRIP`
3. `WIP`
4. For cleaning procmon's output, filtering based on the PID can be used.
5. The registry keys `SOFTWARE\Microsoft\Windows NT\CurrentVersion\Svchost`, `SYSTEM\CurrentControlSet\Services\` serve as host-based indicators.
6. The URL `practicalmalwareanalysis.com` and the user-agent `Windows XP 6.11` are useful network-based signatures.

## Lab 3-3
1. It creates the child process `svchost.exe`. *It uses process replacement.*
2. Nope, the process exits too quickly to display the memory in Process Explorer. Running it in a debugger would be the way to go. *The memory image of the created `svchost.exe` displays some suspicious strings.*
3. A large number of "A" characters could server as a host-based indicator. The only library imported is *kernel32.dll*, and some suspicious imports are `{Write,Read}ProcessMemory` and `CreateProcessA`. *The malware creates a log file named practicalmalwareanalysis.log.*
4. *The program performs process replacement on `svchost.exe` to launch a keylogger.*

## Lab 3-4
1. The process runs the command `"C:\\Windows\\System32\\cmd.exe" /c del C:\\{PATH}\\Lab03-04.exe >> NUL` which eventually deletes the .exe file before exiting.
2. The executable probably needs a certain condition such as a command-line parameter to run its actual payload.
3. It could be run by double clicking on it or by running it through the command line.