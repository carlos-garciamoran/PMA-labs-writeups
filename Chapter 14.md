# Chapter 14 | Malware-Focused Network Signatures
## Lab 14-1
1. The malware uses the networking library `urlmon.dll`. Since this is a high-level library (exporting functions such as `URLDownloadToCacheFileA`), advantages include not having to write low-level function such as opening TCP sockets, not having to worry about following the HTTP standard, etc. *An advantage from using the COM interface is that most of the HTTP requests content comes from Windows itself and cannot be effectively targeted using network signatures.*
2. The beacon URI corresponds to a **Base64-encoded** string. The string is composed by concatenating 6 bytes of the host's **GUID** (`szHwProfileGuid`) and the **username logged-in**. Hence, a different username in the same computer running the malware, or an altogether different computer would change the beacon.
3. The embedded information in the networking beacon can tell the attacker if his malware is already been executed in a computer or/and by a specific user. It **aids in tracking infected hosts**.
4. The malware implements a custom Base64 encoding which uses the `a` character as padding instead of the standard `=`. Still, the indexing string is the standard one.
5. This malware is a **downloader/launcher**. Its overall purpose is to send a beacon to the hardcoded C2 as an infected host and username ID. If the C2 replies, a call to `GetProcess` will follow executing the content of the C2's response —assuming a PE file—, hence dropping additional malware to the system. After this, the malware (i.e., the launcher) quits. If the C2 does not respond to the beacon, then the launcher sleeps for 1 minute before beaconing again.
6. To detect the malware, the following elements could be used to build a network signature. First, the format of the beacon URL is `www.practicalmalwareanalysis.com/%s/%c.png`.  The `%s` placeholder is the Base64 encoding's (with `a` as padding) concatenation of:
	1. 6 hexadecimal bytes separated by a colon, as in `xx:xx:xx:xx:xx:xx`
	2. a hyphen, `-`
	3. the logged-in username —allowed ASCII characters for a Windows username

	Or, what's equivalent: `xx:xx:xx:xx:xx:xx-username`. The `%c` placeholder corresponds to the last character of the Base64 encoded string.
7. When trying to develop a signature for this malware, defenders might be misguided by hard-coding a whole URI for a particular host as a signature. Given that a combination of GUID and username will most likely be unique for each host, this would not be an effective network signature. Similarly, targeting only the domain name would be too vague since the site hosting the malware may be legitimate despite being infected. *In most cases, the Base64 string ends with an `a`, which usually makes the filename appear as `a.png`. However, if the username length is an even multiple of three, both the final character and the filename will depend on the last character in the encoded username. In this case, **the filename is unpredictable**.*
8. The regex signature `/([0-9a-f]{2}\:){5}[0-9a-f]{2}/i` would detect the formatted GUID before encoding. To detect the encoded URL (specifically, the path after the domain name) a regexp like the following could be used:  `/[0-9a-zA-Z\/]{2,}/[a-zA-Z0-9]{1}/i`.

## Lab 14-2
1. The advantages of using a direct IP addres —for an attacker— is that a domain name does not need to be resolved using DNS, which **generates additional traffic**. The disadvantage is that IPs offer **less flexibility** than a domain name, making it harder for an attacker to manage infrastructure.
2. The malware uses the **WinINet API** (`wininet.dll`), which provides a higher level of abstraction than WinSock API. The advantages include:
	1. **blend more effectively** with regular traffic
	2. having built-in HTTP functions such as `InternetOpenURL`
	3. TCP sockets handled by the library, not the developer

	The disadvantages for the attacker include that it is **easier to reverse-engineer** the code, in part due to having to provide a hard-coded User-Agent.
3. The source of the beaconing URL is **hardcoded as a resource** with uID 1: `http://127.0.0.1/tenfour.html`.
4. The malware uses the User-Agent field to exfiltrate data. It does this by encoding its content with a custom Base64 implementation. The listening thread has the hardcoded User-Agent *Internet Surf*.
5. The malware’s initial beacon is the output from running `cmd.exe` in the infected host, AKA the command-shell prompt.
6. It is easy to spot the encoded information and decode it, knowing the indexing string. This allows coming up with network signatures easily. Also, due to HTTP limits it might not permit exfiltrating large amounts of data (?). *Incoming commands are not encoded.*
5. The malware’s encoding scheme is a Base64 implementation using alphabet `WXYZlabcd3fghijko012e456789ABCDEFGHIJKL+/MNOPQRSTUVmn0pqrstuvwxyz`.
6. Communication is terminated when the C2 sends the `exit` command. *When exiting, the malware tries to delete itself.*
7. The purpose of this malware is to install a **backdoor** in the infected host to which it can send commands. The output of these is exfiltrated via the User-Agent field.

## Lab 14-3
1. What hard-coded elements are used in the initial beacon? What elements, if any, would make a good signature? The initial beacon sends The first beacon is to the URL `www.practicalmalwareanalysis.com/start.htm`. This string together with the hardcoded `Accept` and `User-Agent` headers would make a good signature. *The malware author mistakenly adds an additional User-Agent in the actual User-Agent*
2. The hardcoded elements such as the headers described should be targeted.
3. The malware parses the `noscript` HTML tag. This technique allows blending the attacker commands in plain sight.
4. The malware reads commands from the `noscript` tag. The comparison of this string is scrambled and hinders analysis.
5. What type of encoding is used for command arguments? How is it different from Base64, and what advantages or disadvantages does it offer? The enconding for command arguments represents an index in the character set `/[a-z0-9]:.`. Since this scheme is non-standard it makes the malware harder to be analyzed, though it is not complex to reverse-engineer.
6. The commands available are:
	1. sleep
	2. download and run an executable
	3. quit
	4. update the beacon URL
7. The purpose of this malware is to install a downloader allowing the previous commands to be run.
8. For signatures, see question #1.
9. Idem.