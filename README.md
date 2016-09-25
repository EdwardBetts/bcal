# calb

`calb` (*CALculate Bytes*) is a utility for storage conversions and calculations. Storage, hardware and firmware developers work with numerical calculations regularly e.g., storage unit conversions, address calculations etc. If you are one and can't calculate the hex address offset for (512 - 8) MiB immediately, or the value when the 43<sup>rd</sup> bit of a 64-bit address is set, `calb` is for you.

`calb` follows Ubuntu's standard unit conversion and notation [policy](https://wiki.ubuntu.com/UnitsPolicy).

`calb` is written in C and **GPLv3** licensed.

<p align="center">
<a href="https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=RMLTQ76JSXJ4Q"><img src="https://img.shields.io/badge/paypal-donate-orange.svg?maxAge=2592000" alt="Donate" /></a>
</p>

## Table of Contents

- [Features](#features)
- [Installation](#installation)
  - [Installing from this repository](#installing-from-this-repository)
- [Usage](#usage)
  - [cmdline options](#cmdline-options)
  - [Operational notes](#operational-notes)
- [Examples](#examples)
- [Copyright](#copyright)

## Features

- converts to IEC and SI standards
- shows the address in bytes
- shows address as LBA:OFFSET
- converts CHS to LBA and *vice versa*
- shows binary, decimal and hex representation of a number
- supports custom sector size, max heads/cylinder and max sectors/track
- minimal dependencies

## Installation

`calb` needs to be compiled from source. It doesn't have any dependencies other than standard libc.

Run:

    $ make
    $ sudo make install
To uninstall, run:

    $ sudo make uninstall

## Usage

### cmdline options

    usage: calb [-c N] [-s bytes] [-h]
            [N unit]

    Perform storage conversions and calculations.

    positional arguments:
      N unit           capacity in B/KiB/MiB/GiB/TiB/kB/MB/GB/TB
                       see https://wiki.ubuntu.com/UnitsPolicy
                       should be space-separated, case is ignored
                       N can be decimal or '0x' prefixed hex value

    optional arguments:
      -c N             show N in binary, decimal and hex formats
                       N must be non-negative
                       use prefix '0b' for binary, '0x' for hex
      -f FORMAT        convert CHS to LBA or LBA to CHS
                       formats are hyphen-separated
                       LBA format:
                           starts with 'l':
                           lLBA-MAX_HEAD-MAX_SECTOR
                       CHS format:
                           starts with 'c':
                           cC-H-S-MAX_HEAD-MAX_SECTOR
                       omitted values are considered 0
                       FORMAT 'c-50--0x12-' denotes:
                         C = 0, H = 50, S = 0, MH = 0x12, MS = 0
                       FORMAT 'l50-0x12' denotes:
                         LBA = 50, MH = 0x12, MS = 0
                       decimal or '0x' prefixed hex values accepted
                       default MAX_HEAD: 0x10, default MAX_SECTOR: 0x3f
      -s               sector size in decimal or hex bytes [default 0x200]
      -h               show this help and exit

### Operational notes

- Fractional bytes do not exist, because they can't be addressed. `calb` shows the integral floor value for bytes.
- Units can be specified in B/KiB/MiB/GiB/TiB/kB/MB/GB/TB following Ubuntu policy. Ass all of these are unique, `calb` treats specified unit in a case insensitive way.
- Hex and decimal inputs are recognized for all inputs. Binary inputs are supported only for number conversion. This is a provision to convert a binary number to decimal/hex before using it as an input for other operations.
- Prefix hex inputs with `0x`, binary inputs with `0b`.
- Default values:
  - sector size: 0x200 (512)
  - max heads per cylinder: 0x10 (16)
  - max sectors per track: 0x3f (63)
- CHS and LBA inputs for format conversion are hyphen separated. The syntax is:
  - LBA: `lLBA-MAX_HEAD-MAX_SECTOR`   [NOTE: LBA starts with `l` (case ignored)]
  - CHS: `cC-H-S-MAX_HEAD-MAX_SECTOR` [NOTE: CHS starts with `c` (case ignored)]
  Any unspecified value, including the one preceding the first `-` to the one following the last `-`, is considered `0`.
- No negative numbers allowed. Limits are `unsigned long long` and `double`.

## Examples

1. Convert storage capacity to other units and get address, LBA:

        $ calb 20140115 b
        $ calb 0x1335053 B
        $ calb 0xaabbcc kb
        $ calb 0xdef Gib
Note that the units are case-insensitive.

2. Convert storage capacity, set sector size to 4096 to calculate LBA:

        $ calb 0xaabbcc kb -s 4096

3. Convert LBA to CHS:

        $ calb -f l500
        $ calb -f l0x600-18-0x7e
        $ calb -f l0x300-0x12-0x7e

4. Convert CHS to LBA:

        $ calb -f c10-10-10
        $ calb -f c0x10-0x10-0x10
        $ calb -f c0x10-10-2-0x12
        $ calb -f c-10-2-0x12
	$ calb -f c0x10-10--0x12

5. Represent a number in binary, decimal and hex:

        $ calb -c 20140115
        $ calb -c 0b1001100110101000001010011
        $ calb -c 0x1335053

6. Help and additional information:

        $ man calb
        $ calb -h

## Copyright

Copyright (C) 2016 [Arun Prakash Jana](mailto:engineerarun@gmail.com)
