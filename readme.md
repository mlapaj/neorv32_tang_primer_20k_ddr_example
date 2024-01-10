# Neorv32 on Sipeed Tang Primer 20k

This repo contains example project for Neorv32 CPU.
More info about CPU:
[Neorv32 github](https://github.com/stnolting/neorv32)

**As a Bonus, I have added DDR External memory support.**

## Used IP Cores:
- Neorv32 core, taken default bootloader and default ROM/RAM files.
- Gowin PLL,
- Gowin DDR3 Interface

## My implementation
- Wishbone adapter for CPU<>DDR3. I'm FPGA/VDHL learner - code might be not 100% perfect.

## Details:
- UART Pins : TX - R8, RX - T7
- UART Speed: 19200
- CPU Clock at 27 MHZ
- DDR Clock at 200 MHz

## Howto build
For flashing I'm using [openFPGALoader](https://github.com/trabucayre/openFPGALoader).  
I'm not using GOWIN EDA GUI. Makefile is used. You need to setup proper path

For build type `make`  
For flash type `make flash`


## Uart Output
```
<< NEORV32 Bootloader >>

BLDV: Oct 31 2023
HWV:  0x01090100
CLK:  0x019bfcc0
MISA: 0x40901103
XISA: 0x00000ebb
SOC:  0x8003800f
IMEM: 0x00004000
DMEM: 0x00002000

Autoboot in 8s. Press any key to abort.
Aborted.

Available CMDs:
 h: Help
 r: Restart
 u: Upload
 s: Store to flash
 l: Load from flash
 x: Boot from flash (XIP)
 e: Execute
CMD:>
```

## Example app
This is simple app for printing NEORV32 Logo and performs DDR Test - read and write.

Go to sw directory, modify makefile NEORV32_HOME and `make all`.  
You will need to use GCC Toolchain for it.  
Next, using bootloader, we can upload our app.  
All you need to do is to stop bootloader, press `u` key, next upload your app and press `e` key.

I'm using picocom for UART communication  
`sudo picocom /dev/ttyUSB2 -b 19200 --send-cmd="ascii-xfr -s -n"`  
For sendinf files you need to press `Ctrl A` and `Ctrl S`

You will get:
```
CMD:> u
Awaiting neorv32_exe.bin... 
*** file: ./neorv32_exe.bin
$ ascii-xfr -s -n ./neorv32_exe.bin
ASCII upload of "./neorv32_exe.bin"


*** exit status: 0 ***
OK
CMD:> e
Booting from 0x00000000...


                                                                                      ##        ##   ##                                                                                         ##        ##   ##   ##    
 ##     ##   #########   ########    ########   ##      ##   ########    ########     ##      ################  
####    ##  ##          ##      ##  ##      ##  ##      ##  ##      ##  ##      ##    ##    ####            ####
## ##   ##  ##          ##      ##  ##      ##  ##      ##          ##         ##     ##      ##   ######   ##  
##  ##  ##  #########   ##      ##  #########   ##      ##      #####        ##       ##    ####   ######   ####
##   ## ##  ##          ##      ##  ##    ##     ##    ##           ##     ##         ##      ##   ######   ##  
##    ####  ##          ##      ##  ##     ##     ##  ##    ##      ##   ##           ##    ####            ####
##     ##    #########   ########   ##      ##      ##       ########   ##########    ##      ################  
                                                                                      ##        ##   ##   ##    
Hello world! :)
write:
ffffffff 00000001 fffffffd 00000003 fffffffb 00000005 fffffff9 00000007 fffffff7 00000009 fffffff5 0000000b fffffff3 0000000d fffffff1 0000000f ffffffef 00000011 ffffffed 00000013 
dump:
ffffffff 00000001 fffffffd 00000003 fffffffb 00000005 fffffff9 00000007 fffffff7 00000009 fffffff5 0000000b fffffff3 0000000d fffffff1 0000000f ffffffef 00000011 ffffffed 00000013 
write:
fffffffe 00000002 fffffffc 00000004 fffffffa 00000006 fffffff8 00000008 fffffff6 0000000a fffffff4 0000000c fffffff2 0000000e fffffff0 00000010 ffffffee 00000012 ffffffec 00000014 
dump:
fffffffe 00000002 fffffffc 00000004 fffffffa 00000006 fffffff8 00000008 fffffff6 0000000a fffffff4 0000000c fffffff2 0000000e fffffff0 00000010 ffffffee 00000012 ffffffec 00000014 
write:
(....)

```

## Todo list:
- Faster DDR. Currently clocked at 200, I have brought up 400 MHz in different project.
- Try to start U-Boot

## Other
Issues, ideas => feel free and issue a ticket or contribute


## Resource usage
Note: this is resource usage NEORV32 + default DMEM & IMEM + DDR Controller
```
  ----------------------------------------------------------
  Resources                   | Usage
  ----------------------------------------------------------
  Logic                       | 9940/20736  48%
    --LUT,ALU,ROM16           | 9142(8279 LUT, 863 ALU, 0 ROM16)
    --SSRAM(RAM16)            | 133
  Register                    | 6089/16173  38%
    --Logic Register as Latch | 0/15552  0%
    --Logic Register as FF    | 6082/15552  40%
    --I/O Register as Latch   | 0/621  0%
    --I/O Register as FF      | 7/621  2%
  CLS                         | 6852/10368  67%
  I/O Port                    | 59
  I/O Buf                     | 56
    --Input Buf               | 7
    --Output Buf              | 31
    --Inout Buf               | 18
  IOLOGIC                     | 58%
    --IDES8_MEM               | 16
    --OSER8                   | 24
    --OSER8_MEM               | 20
    --IODELAY                 | 16
  BSRAM                       | 61%
    --SP                      | 12
    --SDPB                    | 7
    --SDPX9B                  | 4
    --pROMX9                  | 5
  DSP                         | 17%
    --MULT36X36               | 2
  PLL                         | 1/4  25%
  DCS                         | 0/8  0%
  DQCE                        | 0/24  0%
  OSC                         | 0/1  0%
  CLKDIV                      | 1/8  13%
  DLLDLY                      | 0/8  0%
  DQS                         | 2/9  23%
  DHCEN                       | 1/16  7%
  ==========================================================
```
