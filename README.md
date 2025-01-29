# Compiling and flashing WCH RISC-V products on Windows

## Prerequisites
Parts needed from MounRiver's directory:
- WCH's modified RISC-V toolchain (gcc, objcopy, gdb)
```
{Moun River directory}/MounRiver_Studio/toolchain/RISC-V Embedded GCC12/bin
```
- files for specified MCU:
    - header
    - core_riscv header and source
    - startup file (comment out jump to SystemInit to avoid default clock settings)
    - linker
    - svd file for debugging
```
unzip necessary folder in {Moun River directory}/MounRiver/MounRiver_Studio/template/wizard/WCH/RISC-V/{MCU name}/NoneOS
```

- modified OpenOCD and wch-riscv config
```
{Moun River directory}/MounRiver_Studio/toolchain/OpenOCD/bin
```

Other:
- download [wlink](https://github.com/ch32-rs/wlink)

## Compile
WCH's modified RISC-V gcc is needed to compile C code. This compiler uses parameter -march=rv32imacxw which permits use of "WCH-Interrupt-fast" attribute with interrupts.  

Compile your application. As the startup file is written in assembly, files have to be compiled separately.
```
riscv-none-elf-gcc {source_name}.c -o {source_name}.o -c -march=rv32imacxw -mabi=ilp32
```

Compile startup file.
```
riscv-none-elf-gcc {startup_name}.c -o {startup_name}.o -c -march=rv32imacxw -mabi=ilp32
```

Use linker.
```
riscv-none-elf-gcc *.o -T {linker_name}.ld -o {output}.elf -nostartfiles
```

Create executable.
```
riscv-none-elf-objcopy {output}.elf -O ihex {hex_output}.hex
```

## Upload
Upload code through the WCH-Link debug probe.
```
wlink erase && wlink flash {hex_output} -v
```
In my case erasing already flashed firmware is necessary for upload to work.

## Debug in Visual Studio Code
[Cortex Debug](https://github.com/Marus/cortex-debug) extension can be configured to work with RISC-V microcontrollers.  

Create *launch.json* file with these settings:
- path to gdb and objdump, as Cortex Debug looks for arm gcc tools by default
```
"gdbPath": "{Moun River directory}/MounRiver_Studio/toolchain/RISC-V Embedded GCC12/bin/riscv-none-elf-gdb.exe",
"objdumpPath": "{Moun River directory}/MounRiver_Studio/toolchain/RISC-V Embedded GCC12/bin/riscv-none-elf-objdump.exe"
```
- specify use of OpenOCD
```
"servertype": "openocd"
```
- include config
```
"configFiles": ["{Moun River directory}/MounRiver_Studio/toolchain/OpenOCD/bin/wch-riscv.cfg"]
```
- insert svd file to list peripherals while debugging
```
"svdFile": "{Moun River directory}/MounRiver_Studio/template/wizard/WCH/RISC-V/{MCU name}/NoneOS/{svd file}"
```

### Note

Add following flags while compiling in order for the executable to be debuggable
```
-ggdb -O0
``` 

## TODO
- Break out of MounRiver's dependency by writing own startup file and linker
- Use standard gcc