## qubic-computor-build-guide-windows

### A step-by-step guide to build qubic computor binaries from scratch

I hacked together another step-by-step guide for compiling Computor Based on version of @crypdro and @Chris Dukakis on windows with some modifications  

Based on original guide: https://github.com/paulhandy/hellocomputor

#### Build computor by yourself on Win Machines
- Open Powershell as Admin 
- Run: ```Set-ExecutionPolicy Bypass -Scope Process```
    - Answer yes
- Run: ```Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))```
- Run: ```choco install make```
- Run: ```choco install llvm -y```
- Run: ```choco install qemu -y```
- Run: ```setx /M PATH "$Env:PATH;C:\Program Files\qemu;C:\Program Files\LLVM\bin"```
- Run: ```choco install git -y```
- Reboot System! **I am serious - you shold better do this - if you omit this and you just reopen powershell or only do an refreshenv chances are very high building below does not work. So you better take this short break and let your machine process all those new software installs.**
- Open Powershell as Admin
- Run: ```git clone https://github.com/paulhandy/hellocomputor.git```
- Run: ```cd hellocomputor```
- Run: ```git submodule update --init --recursive```
- Run: ```make -f .mk qemu``` (currently non-working go to next step below)

#### Build by yourself:
- Open Powershell
- Run: ```cd hellocomputor```
- Run: ```clang -target x86_64-unknown-windows -ffreestanding -fshort-wchar -mno-red-zone -Iextern/efi -o hellocomputor.o -c hellocomputor.c```
- Run: ```clang -target x86_64-unknown-windows -nostdlib "-Wl,-entry:efi_main" "-Wl,-subsystem:efi_application" -fuse-ld=lld-link -o MYQUBIC.efi hellocomputor.o```
- Your compiled file will placed in the current directory and named: MYQUBIC.efi 

#### Run in Qemu
- Open Powershell
- Run: ```cd hellocomputor```
- Run: ```mkdir boot```
- Run: ```copy MYQUBIC.efi boot```
- Run: ```qemu-system-x86_64 -drive if=pflash,format=raw,readonly=on,file=ovmf/OVMF_CODE.fd -drive if=pflash,format=raw,file=ovmf/OVMF_VARS.fd -drive index=0,format=raw,file=fat:rw:BOOT```
- Wait for Qemu to Boot
- Run in Qemu: ```FS0:```
- Run in Qemu: ```MYQUBIC.efi```
