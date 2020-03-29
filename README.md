# nes-util
Various utilities related to the [Nintendo Entertainment System](http://en.wikipedia.org/wiki/Nintendo_Entertainment_System).

## ineslib.py
```
NAME
    ineslib

DESCRIPTION
    A library for parsing/encoding iNES ROM files (.nes).
    See http://wiki.nesdev.com/w/index.php/INES

FUNCTIONS
    create_iNES_header(PRGSize, CHRSize, mapper=0, mirroring='h', saveRAM=False)
        Return a 16-byte iNES header as bytes. On error, raise an exception with an error message.
        PRGSize: PRG ROM size (16 * 1024 to 4096 * 1024 and a multiple of 16 * 1024)
        CHRSize: CHR ROM size (0 to 2040 * 1024 and a multiple of 8 * 1024)
        mapper: mapper number (0-255)
        mirroring: name table mirroring ('h'=horizontal, 'v'=vertical, 'f'=four-screen)
        saveRAM: does the game have save RAM

    parse_iNES_header(handle)
        Parse an iNES header. Return a dict. On error, raise an exception with an error message.
```

## nesgenielib.py
```
NAME
    nesgenielib - A library for decoding and encoding NES Game Genie codes.

FUNCTIONS
    decode_code(code)
        Decode a Game Genie code.
        If an eight-letter code, return (address, replacement_value, compare_value).
        If a six-letter code, return (address, replacement_value).
        If an invalid code, return None.

    encode_code(address, replacement, compare=None)
        Encode a Game Genie code.
        address: 16-bit int, replacement/compare: replacement value and compare value (8-bit ints)

    parse_values(input_)
        Parse 'aaaa:rr' or 'aaaa?cc:rr' where aaaa = address in hexadecimal, rr = replacement value
        in hexadecimal, cc = compare value in hexadecimal. Return the values as a tuple of integers,
        with the replacement value before the compare value, or None if the input matches neither.

    stringify_values(address, replacement, compare=None)
        Convert the address, replacement value and compare value into a hexadecimal representation
        ('aaaa:rr' or 'aaaa?cc:rr').
```

The structure of NES Game Genie codes:
* The codes consist of the following 16 letters: `A P Z L G I T Y E O X U K S V N`
* The codes are six or eight letters long (e.g. `SXIOPO`, `YEUZUGAA`).
* In canonical codes, the third letter reflects the length of the code:
  * In six-letter codes, the letter is one of `A P Z L G I T Y`
  * In eight-letter codes, the letter is one of `E O X U K S V N`
* The Game Genie and my programs accept non-canonical codes too.
* All codes encode a 15-bit address (NES CPU ROM `0x8000-0xffff`) and a "replacement value" (`0x00-0xff`).
* Eight-letter codes also contain a "compare value" (`0x00-0xff`).

## ines_combine.py
```
usage: ines_combine.py [-h] -p PRG_ROM [-c CHR_ROM] [-m MAPPER] [-n {h,v,f}] [-s] outputFile

Create an iNES ROM file (.nes) from PRG ROM and CHR ROM data files.

positional arguments:
  outputFile            The iNES ROM file (.nes) to write.

optional arguments:
  -h, --help            show this help message and exit
  -p PRG_ROM, --prg-rom PRG_ROM
                        PRG ROM data file; required; the size must be 16-4096 KiB and a multiple of 16 KiB. (default:
                        None)
  -c CHR_ROM, --chr-rom CHR_ROM
                        CHR ROM data file; the size must be 0-2040 KiB and a multiple of 8 KiB. If the file is empty
                        or the argument is omitted, the game uses CHR RAM. (default: None)
  -m MAPPER, --mapper MAPPER
                        Mapper number (0-255). (default: 0)
  -n {h,v,f}, --mirroring {h,v,f}
                        Type of name table mirroring: h=horizontal, v=vertical, f=four-screen. (default: h)
  -s, --save-ram        The game contains battery-backed PRG RAM at $6000-$7fff. (default: False)
```

## ines_info.py
Print information of an iNES ROM file (.nes) in CSV format. Argument: file. Output fields: file, size, PRG ROM size, CHR ROM size, mapper, name table mirroring, does the game have save RAM, trainer size, file MD5 hash, PRG ROM MD5 hash, CHR ROM MD5 hash.

## ines_split.py
```
usage: ines_split.py [-h] [-p PRG] [-c CHR] input_file

Extract PRG ROM and/or CHR ROM data from an iNES ROM file (.nes).

positional arguments:
  input_file         The iNES ROM file (.nes) to read.

optional arguments:
  -h, --help         show this help message and exit
  -p PRG, --prg PRG  The file to extract PRG ROM data to (16-4096 KiB).
  -c CHR, --chr CHR  The file to extract CHR ROM data to (8-2040 KiB).

Specify at least one output file.
```

## nes_chr_conv.py
Requires the [PyPNG module](http://github.com/drj11/pypng). TODO: switch to Pillow (more file formats).
```
usage: conv.py [-h] [-p PALETTE PALETTE PALETTE PALETTE] {e,d} input_file output_file

Convert an NES CHR (graphics) data file to a PNG file or vice versa. File restrictions (input&output): PNG: width 128
pixels, height a multiple of 8 pixels, 4 unique colors or less, all colors specified by --palette; CHR: file size a
multiple of 256 bytes.

positional arguments:
  {e,d}                 What to do: e=encode (PNG to CHR), d=decode (CHR to PNG).
  input_file            The file to read (PNG if encoding, CHR if decoding).
  output_file           The file to write (CHR if encoding, PNG if decoding).

optional arguments:
  -h, --help            show this help message and exit
  -p PALETTE PALETTE PALETTE PALETTE, --palette PALETTE PALETTE PALETTE PALETTE
                        PNG palette (which colors correspond to CHR colors 0-3). Four 6-digit hexadecimal RRGGBB color
                        codes ("000000"-"ffffff") separated by spaces. Must be all distinct when encoding (reading a
                        PNG). (default: ('000000', '555555', 'aaaaaa', 'ffffff'))
```

## nesgenie.py
Encode and decode NES Game Genie codes. Argument: six-letter code, eight-letter code, aaaa:rr or aaaa?cc:rr (aaaa = address in hexadecimal, rr = replacement value in hexadecimal, cc = compare value in hexadecimal).

## nesgenie_6to8.py
Convert a 6-letter NES Game Genie code into 8 letters using the iNES ROM file (.nes). Args: file code
