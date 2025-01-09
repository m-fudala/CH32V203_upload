# Compiling and flashing for WCH RISC-V products on Windows

## Compile
[riscv-none-elf-gcc](https://github.com/xpack-dev-tools/riscv-none-elf-gcc-xpack) is used to compile C code.

### Install
link https://xpack-dev-tools.github.io/riscv-none-elf-gcc-xpack/
- Install [Node.js](https://nodejs.org/en)
- Install xpm through npm/Node.js
```
npm install --location=global xpm@latest
```
- Install gcc
```
xpm install @xpack-dev-tools/riscv-none-elf-gcc@latest --verbose
```

### Compile
To program bare-metal WCH's header for specified MCU and core_riscv files are needed.
```
riscv-none-elf-gcc {source path} -o {elf output path} -march=rv32imac -mabi=ilp32 -c -Os -v
```
MounRiver uses -march=rv32imacxw with their modified gcc, but apparently it only allows to use "WCH-Interrupt-fast" attribute with interrupts. Code can also be compiled with their gcc which is located in MounRiver install folder. \
-c option is needed for some reason.
```
riscv-none-elf-objcopy {elf path} -O ihex {hex output path} -v
```

## Upload
Download [wlink](https://github.com/ch32-rs/wlink) and use it with the WCH-Link debug probe.
```
wlink erase && wlink flash {hex path} -v
```
In my case erasing already flashed firmware is necessary for upload to work.

## TODO
- Use make
- Debugging