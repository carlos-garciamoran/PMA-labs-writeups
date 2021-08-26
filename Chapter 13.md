# Chapter 13 | Data Encoding
## Lab 13-1
1. The notable string in the output of `strings` is `ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/`, which most likely works as the indexing string of base64-encoded data. Most importantly, the string `www.practicalmalwareanalysis.com` is found during dynamic analysis at address `401402`. This corresponds to the decoded text `LLL [...]` which is stored as a resource of type UNICODE. The URL path also found in dynamic analysis is `VXNlci1QQw=`.
2. The encoding used is **single-byte XOR**.
3. The key used for encoding is 0x3B. The content (resource with index 101) decodes to the URL `www.practicalmalwareanalysis.com` which most likely is a C2 server.
4. Using KANAL through PEiD, a Base64 table is detected, displaying multiple references in function `sub_401000`. Using DIE, the `.text` section displays a high entropy value, potentially being packed or containing encrypted content.
5. The malware uses standard Base64 for encoding the URL's path.
6. The Base64 function is located at `sub_4010B1` in the disassembly.    
7. The GET path will be no longer than 12 bytes. *The GET path will be no longer than 12 bytes decoded, so 16 characters when Base64-encoded.*
8. ~~The padding characters (`=` and `==`) are removed from the Base64-encoded data.~~ *Padding characters may be used if the hostname length is less than 12 bytes and not evenly divisible by 3.*
9. This malware contacts a host which domain is stored in the resource section encrypted with key 0x3B. The path requested is the computer's hostname (trimmed to 12 bytes) encoded with standard Base64. If the host responds with the character `o`, the malware exits; else it sleeps for 35 seconds before trying again `loc_40142E`.

## Lab 13-2
1. Every 10 seconds, a file starting with `temp` and 8 random hexadecimal characters is created. It weighs about 4 MB and contains encoded/encrypted data.
2. KANAL does not find any known crypto signature and the file's entropy is standard. An XOR search returns multiple hits such as `xor eax, ecx` in `sub_401739`, `xor eax, [ebp+var_10]` in `sub_40128D`, and `xor eax, [esi+edx*4]` in `sub_401570`. Still, no key is found via this method —there's no XOR with a constant, all XOR's are between registers or registers and variables.
3. Looking for `WriteFile` calls would be interesting for finding the encoding functions.
4. The encoding function is at `sub_40128D` ~~starts at `sub_401739`~~.
5. The source of the encoded content is located at `sub_401070`, where a variety of system information is collected. *The source content is a screenshot.*
6. The algorithm used for encoding starts at `sub_401739`, *however since it is nonstandard it is not easy to determine. The best way to decode the data is via instrumentation.*
7. The original source of the encoded files can be retrieved by opening the program with **x32dbg** and following these steps:
	1. **set a breakpoint** before the encryption subroutine gets called
	2. run the program
	3. inspect the values passed to the stack (buffer length and a pointer to the buffer, respectively)
	4. right-click on the pointer to the buffer and click **Follow in Dump**
	5. selecting the whole buffer in the stack —knowing the buffer size
	6. copying the **hexadecimal dump** of one of the encrypted files
	7. pasting the dump by right-clicking on the buffer selection, hovering into *Binary*, and clicking *Paste*
	8. set a breakpoint after the  `WriteFile` wrapper gets called
	9. run the program
	10. set the extension to BMP
	11. open the decrypted file!

## Lab 13-3
1. With static analysis, a custom Base64 index string and a plaintext URL are found. The function at `40103F` receives data to be encoded with Base64.
2. The search returns a lot of results, none of which show encoding with a hardcoded key.
3. The KANAL and DIE plugins show use of the AES Rijndael cypher. The entropy graph shows high values at the beginning of the `.rdata` section, potentially due to the presence of the AES cryptographic constants. The XORs found in the previous question are most likely part of the AES code.
4. The malware uses custom Base64 encoding and AES encryption.
5. The custom Base64 index string is  `CDEFGHIJKLMNOPQRSTUVWXYZABcdefghijklmnopqrstuvwxyzab0123456789+/`. The AES key is `ijklmnopqrstuvwx`.
6. For Base64, the index string is sufficient. For AES, in addition to the key itself, the key size and block size must be known for the encryption algorithm. *For AES, the variables that may be needed are the key-generation algorithm if one is used, the key size, the mode of operation, and the initialization vector if one is needed.*
7. The malware sets up a reverse `cmd.exe` TCP shell on port 8910 with a C2 at `www.practicalmalwareanalysis.com`. All traffic in and outbound is encrypted with AES. Input and output is handled with 2 threads and 2 pipes, one for STDIN and one for STDOUT. *Incoming commands are decoded using the custom Base64 cipher and outgoing command-shell responses encrypted with AES*.
8. The content is the traffic of the reverse shell. Commands sent as input and results sent back as output.