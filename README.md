# Compiling and flashing for WCH RISC-V products on Windows

## Compilation
WCH's modified RISC-V gcc is used to compile C code. The compiler can be found in MounRiver's directory.

### Compile
Files from MounRiver needed to program bare-metal:
- WCH's header for specified MCU
- core_riscv files
- startup file (comment out jump to SystemInit to avoid default clock settings)
- linker

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
Download [wlink](https://github.com/ch32-rs/wlink) and use it with the WCH-Link debug probe.
```
wlink erase && wlink flash {hex_output} -v
```
In my case erasing already flashed firmware is necessary for upload to work.

## TODO
- Use make
- Debugging
- Break out of MounRiver's dependency by writing own startup file and linker
- Use standard gcc