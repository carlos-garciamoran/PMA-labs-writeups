# Chapter 18 | Packers and Unpacking
## Lab 18-1
- Sample is packed with a **modified UPX** implementation
- **Tail jump** located at `409F43`
	- found by spotting odd `jmp` to pointer far away from code located in the `.text` section
- **OEP** located at `40154F`
- Sample unpacked by
	1. **placing breakpoint at OEP**
	2. **dumping memory** to disk via x32dbg's **Scylla** plugin
- Sample corresponds to [Lab 14.1](obsidian://open?vault=Obsidian&file=PMA%20Labs%2FChapter%2014)

## Lab 18-2
- Sample's packer is **FGS**
- **Tail jump** located at `4050E1`
- **OEP** located at `401090`
- Sample corresponds to [Lab 7.2](obsidian://open?vault=Obsidian&file=PMA%20Labs%2FChapter%207%20_%20R)

## Lab 18-3
- Sample's packer is **PEProtect**
- **Tail jump** located at `407556`
- **OEP** located at `401577`
- Sample corresponds to [Lab 9.2](obsidian://open?vault=Obsidian&file=PMA%20Labs%2FChapter%209)

## Lab 18-4
- Sample's packer is **ASPack**
- **Tail jump** located at `4113BF`
- **OEP** located at `403896`
- Sample corresponds to [Lab 9.1](obsidian://open?vault=Obsidian&file=PMA%20Labs%2FChapter%209)

## Lab 18-5
- Sample's packer is **[Win]Upack**
- **Tail jump** located at ``
- **OEP** located at ``
- Sample corresponds to [Lab 7.1](obsidian://open?vault=Obsidian&file=PMA%20Labs%2FChapter%207)