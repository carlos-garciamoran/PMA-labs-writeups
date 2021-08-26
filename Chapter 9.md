# Chapter 9 | Debugging with OllyDBG
## Lab 9-1 | WIP
1. The malware can be installed either by patching the binary to bypass the checks or by inputting `-in` and the password `abcd` as CLI arguments.
2. The accepted CLI arguments are `-in`, `-re`, `-c`, and `-cc`. To use any of these options the password `abcd` must be input as the last argument. Usually, if any of these arguments is not called with the appropriate arguments, it calls the function at `402410` which deletes the file executing the code. The following is an outline of the CLI options:
    - `-in` takes 1 optional argument and performs the installation:
		- create a service which has the name passed as a CLI argument or the exe's filename;
		- copy the running file to a different location;
		- modify `kernel32.dll`'s created, last accessed, or last modified timestamps;
		- store the string "ups http://www.practicalmalwareanalysis.com 80 60" into the registry `SOFTWARE\Microsoft \XPS\Configuration`. This clearly looks like the C2 default configuration, representing a password or command, URL, HTTP port, and contact time interval, respectively;
    - `-re` takes 1 optional argument does and performs the cleanup:
		- delete the service created via `-in`, using the passed argument as the service name;
		- delete the file containing the malware;
		- delete the registry value `SOFTWARE\Microsoft \XPS\Configuration`;
	- `-c` takes 4 arguments. It only sets the `SOFTWARE\Microsoft \XPS\Configuration` value to the 4 arguments passed (concatenated). This is most likely used to change the C2 config outlined above;
	- `-cc` takes 0 arguments and prints the contents of `SOFTWARE\Microsoft \XPS\Configuration`.
3. The instruction at `402B61`, `jnz short 402BC7`, could be replaced by an unconditional jump such as `jmp short 402BC7`. This way the password wouldn't be required.
4. Host-based indicators include the registry key `SOFTWARE\Microsoft \XPS` with value name `Configuration` and a service name containing the string *Manager Service*. -c 13 37 URL.com COMMAND abcd -c COMMAND C2.COM 80 60 abcd
5. **WIP** Sleep for a certain amount of time, upload a file from the infected host to the C2, download a file from the C2 to the infected host, execute a command and send its output back to the C2, and do nothing.
6. Network-based signature: URL http://www.practicalmalwareanalysis.com and HTTP command `GET {PATH} HTTP/1.0 \r\n\r\n`.

## Lab 9-2 | TODO


## Lab 9-3 | TODO
