# Verification of factory access dongle for AIT Luxtronik

You can buy a USB dongle that provides access to more settings on the Luxtronik heat pump controller. But as the cautious buyer you are, you want to verify the integrity of the dongle before you buy one. This guide shows how to verify that the dongle is in working condition and not a fake.

First, you need these hardware identifiers about the USB dongle:
- Vendor ID (VID) (e.g. 058f)
- Product ID (PID) (e.g. 6387)
- Manufacturer string (e.g. "Generic") (make sure you include any possible leading spaces)
- Product string (e.g. "Mass Storage")
- Serial number (e.g. "9A0EFE9E") (if the serial is longer than 64 characters the dongle will not work)

You can use [USBTreeView](https://www.uwe-sieber.de/usbtreeview.html#download) on Windows to read these properties. On Linux you can use lsusb -v -d.

In the root directory of the dongle, you should see a single file:

>  \{9A0EFE9E}

The name of the file must correspond to the serial number enclosed in curly braces, or else your dongle is not genuine. The size of the file must be exactly 448 bytes.

The content of the file can be verified as follows (example dump):

    00000000  30 30 30 30 30 30 30 61  20 41 6c 70 68 61 2d 49  |0000000a Alpha-I|
    00000010  6e 6e 6f 54 65 63 20 47  6d 62 48 0a 00 00 00 00  |nnoTec GmbH.....|
    00000020  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
    00000030  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
    00000040  38 25 a0 f5 8b 4d 3b b1  84 1a 51 52 80 40 33 fe  |8%...M;...QR.@3.|
    00000050  3b 8c 56 22 73 3e 60 33  4c 77 f4 14 d2 7f 0b 8a  |;.V"s>`3Lw......|
    00000060  25 e0 d8 c3 a0 7c fa 5b  8d 7d 02 e1 96 fe 80 47  |%....|.[.}.....G|
    00000070  99 96 05 96 3d 04 90 93  4c 96 7c 2f 01 16 51 7d  |....=...L.|/..Q}|
    00000080  14 fe 59 c5 3e 1a e4 05  56 17 23 2a 32 f8 ee 5c  |..Y.>...V.#*2..\|
    00000090  48 89 37 e6 ce 09 77 c2  09 57 10 cf 03 d2 5f 8d  |H.7...w..W...._.|
    000000a0  11 cf da 64 08 b8 10 be  a0 db ca db 70 a3 99 3d  |...d........p..=|
    000000b0  f3 f3 69 3b e2 3a 65 98  a1 4d e7 87 e3 d5 47 04  |..i;.:e..M....G.|
    000000c0  0f fe 5a e3 80 62 e1 7e  6a eb 31 47 6d 2e 62 87  |..Z..b.~j.1Gm.b.|
    000000d0  16 87 c5 67 85 23 07 29  6f 0c df d5 ba 51 0d 50  |...g.#.)o....Q.P|
    000000e0  57 ac 22 88 94 87 46 5b  aa 9b b6 af e7 de f8 74  |W."...F[.......t|
    000000f0  c2 6e 9c cf de 43 b1 7c  69 55 42 68 ff eb 6a 84  |.n...C.|iUBh..j.|
    00000100  4e 30 15 2c e7 47 48 36  45 93 a4 05 80 e8 23 70  |N0.,.GH6E.....#p|
    00000110  92 53 d6 ce ee 7c 6d 39  0d 91 42 f1 9b 7f 3a fe  |.S...|m9..B...:.|
    00000120  cf 5a 45 89 14 de 2f c1  54 14 d5 03 a9 27 93 70  |.ZE.../.T....'.p|
    00000130  37 a4 7b 7f 97 25 e5 6a  5c 6f d3 7a a3 3a ef 9b  |7.{..%.j\o.z.:..|
    00000140  22 e8 b3 e8 2c d6 48 b0  e6 d9 2d 0e 87 55 58 0e  |"...,.H...-..UX.|
    00000150  98 b5 bf 70 d1 5c 42 2d  1c e5 aa 37 ff be 11 59  |...p.\B-...7...Y|
    00000160  84 20 1a 38 56 22 96 81  ec d0 49 8f f5 a1 a5 65  |. .8V"....I....e|
    00000170  cc 6b 4e 07 8a 1b 2d 35  01 97 95 d1 25 12 f9 e9  |.kN...-5....%...|
    00000180  69 df bb a3 01 74 61 fe  b4 e6 68 e6 5f e7 d3 2a  |i....ta...h._..*|
    00000190  33 a7 9d 34 15 c0 11 b1  61 ba 61 d6 86 0f 98 51  |3..4....a.a....Q|
    000001a0  23 e8 3d 42 3c 5c b8 94  a0 8d d7 cf 67 f2 77 fc  |#.=B<\......g.w.|
    000001b0  4d 9a 54 f0 1a 63 9b c0  4e b6 90 0c df cf 7a 86  |M.T..c..N.....z.|
    000001c0

The first 64 bytes (0x00 to 0x39) must exactly match what you see in the dump above.

Then you take the SHA512 hash of the first 64 bytes:

> printf "0000000a Alpha-InnoTec GmbH\n" | dd bs=64 count=1 conv=sync 2>/dev/null | openssl dgst -sha512 -binary | hexdump -C
>
> 
    00000000  38 3b 80 56 4c 0b a0 02  99 90 01 59 56 ee ce 10  |8;.VL......YV...|
    00000010  11 10 70 69 a1 47 80 31  16 07 ba 22 aa f8 de 42  |..pi.G.1..."...B|
    00000020  4e 48 80 d6 0d 3a 14 d5  37 e5 a3 b3 e6 58 d1 aa  |NH...:..7....X..|
    00000030  84 96 f5 4e 01 f9 01 68  33 11 86 3d a0 77 1a 90  |...N...h3..=.w..|
    00000040

and compare each hash byte to every 6th byte of the dongle file starting from **0x40**:

    00000000  30 30 30 30 30 30 30 61  20 41 6c 70 68 61 2d 49  |0000000a Alpha-I|
    00000010  6e 6e 6f 54 65 63 20 47  6d 62 48 0a 00 00 00 00  |nnoTec GmbH.....|
    00000020  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
    00000030  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
    00000040  38 .. .. .. .. .. 3b ..  .. .. .. .. 80 .. .. ..  |8%...M;...QR.@3.|
    00000050  .. .. 56 .. .. .. .. ..  4c .. .. .. .. .. 0b ..  |;.V"s>`3Lw......|
    00000060  .. .. .. .. a0 .. .. ..  .. .. 02 .. .. .. .. ..  |%....|.[.}.....G|
    00000070  99 .. .. .. .. .. 90 ..  .. .. .. .. 01 .. .. ..  |....=...L.|/..Q}|
    etc.

If they are the same, good.

Then take the 4-byte little endian equivalent of the vendor ID and generate another hash:

> printf \\x8f\\x05\\x00\\x00 | openssl dgst -sha512 -binary | hexdump -C

    00000000  25 b1 40 22 77 8a 7c e1  96 93 16 c5 17 5c 09 cf  |%.@"w.|......\..|
    00000010  cf be a3 3b 4d 04 62 47  87 29 51 88 9b 74 43 68  |...;M.bG.)Q..tCh|
    00000020  30 36 e8 ce 91 fe de 03  a4 6a 3a e8 d9 0e 5c 37  |06.......j:...\7|
    00000030  20 81 a1 07 97 e9 74 e6  a7 b1 0f 42 8d fc 63 0c  | .....t....B..c.|
    00000040

and compare each hash byte to every 6th byte of the dongle file starting from **0x41**:

    00000000  30 30 30 30 30 30 30 61  20 41 6c 70 68 61 2d 49  |0000000a Alpha-I|
    00000010  6e 6e 6f 54 65 63 20 47  6d 62 48 0a 00 00 00 00  |nnoTec GmbH.....|
    00000020  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
    00000030  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
    00000040  .. 25 .. .. .. .. .. b1  .. .. .. .. .. 40 .. ..  |8%...M;...QR.@3.|
    00000050  .. .. .. 22 .. .. .. ..  .. 77 .. .. .. .. .. 8a  |;.V"s>`3Lw......|
    00000060  .. .. .. .. .. 7c .. ..  .. .. .. e1 .. .. .. ..  |%....|.[.}.....G|
    00000070  .. 96 .. .. .. .. .. 93  .. .. .. .. .. 16 .. ..  |....=...L.|/..Q}|
    etc.

next, do the same for the 4-byte little endian equivalent of the product ID:

> printf \\x87\\x63\\x00\\x00 | openssl dgst -sha512 -binary | hexdump -C

    00000000  a0 84 33 73 f4 25 fa 96  05 4c 51 3e 23 48 77 03  |..3s.%...LQ>#Hw.|
    00000010  da a0 99 e2 e7 0f e1 6d  c5 6f 0d 94 b6 c2 b1 ff  |.......m.o......|
    00000020  15 45 23 ee 42 cf 2f a9  7b 5c ef 2c 2d 98 42 ff  |.E#.B./.{\.,-.B.|
    00000030  1a ec a5 8a 95 69 61 5f  9d 61 98 3c d7 4d 9b df  |.....ia_.a.<.M..|
    00000040

and compare each hash byte to every 6th byte of the dongle file starting from **0x42**:

    00000000  30 30 30 30 30 30 30 61  20 41 6c 70 68 61 2d 49  |0000000a Alpha-I|
    00000010  6e 6e 6f 54 65 63 20 47  6d 62 48 0a 00 00 00 00  |nnoTec GmbH.....|
    00000020  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
    00000030  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
    00000040  .. .. a0 .. .. .. .. ..  84 .. .. .. .. .. 33 ..  |8%...M;...QR.@3.|
    00000050  .. .. .. .. 73 .. .. ..  .. .. f4 .. .. .. .. ..  |;.V"s>`3Lw......|
    00000060  25 .. .. .. .. .. fa ..  .. .. .. .. 96 .. .. ..  |%....|.[.}.....G|
    00000070  .. .. 05 .. .. .. .. ..  4c .. .. .. .. .. 51 ..  |....=...L.|/..Q}|
    etc.

next, create a hash of the 64-byte 0-padded manufacturer string:

> printf "Generic" | dd bs=64 count=1 conv=sync 2>/dev/null | openssl dgst -sha512 -binary | hexdump -C

    00000000  f5 1a fe 3e 14 e0 5b fe  96 96 7d 1a 2a 89 c2 d2  |...>..[...}.*...|
    00000010  64 db 3d 3a 87 fe 7e 2e  67 0c 50 87 af 6e 7c eb  |d.=:..~.g.P..n|.|
    00000020  2c 93 70 7c f1 5a c1 27  7f 6f 9b d6 0e b5 2d be  |,.p|.Z.'.o....-.|
    00000030  38 d0 65 1b d1 df fe e7  34 ba 51 5c cf 9a c0 cf  |8.e.....4.Q\....|
    00000040

and compare each hash byte to every 6th byte of the dongle file starting from **0x43**:

    00000000  30 30 30 30 30 30 30 61  20 41 6c 70 68 61 2d 49  |0000000a Alpha-I|
    00000010  6e 6e 6f 54 65 63 20 47  6d 62 48 0a 00 00 00 00  |nnoTec GmbH.....|
    00000020  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
    00000030  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
    00000040  .. .. .. f5 .. .. .. ..  .. 1a .. .. .. .. .. fe  |8%...M;...QR.@3.|
    00000050  .. .. .. .. .. 3e .. ..  .. .. .. 14 .. .. .. ..  |;.V"s>`3Lw......|
    00000060  .. e0 .. .. .. .. .. 5b  .. .. .. .. .. fe .. ..  |%....|.[.}.....G|
    00000070  .. .. .. 96 .. .. .. ..  .. 96 .. .. .. .. .. 7d  |....=...L.|/..Q}|
    etc.

next, create a hash of the 64-byte 0-padded product string:

> printf "Mass Storage" | dd bs=64 count=1 conv=sync 2>/dev/null | openssl dgst -sha512 -binary | hexdump -C

    00000000  8b 51 3b 60 d2 d8 8d 80  3d 7c 14 e4 32 37 09 5f  |.Q;`....=|..27._|
    00000010  08 ca f3 65 e3 5a 6a 62  85 df 57 46 e7 9c 69 6a  |...e.Zjb..WF..ij|
    00000020  e7 a4 92 6d 9b 45 54 93  97 d3 22 48 87 bf 1c 11  |...m.ET..."H....|
    00000030  56 49 cc 2d 25 bb b4 d3  15 61 23 b8 67 54 4e 7a  |VI.-%....a#.gTNz|
    00000040

and compare each hash byte to every 6th byte of the dongle file starting from **0x44**:

    00000000  30 30 30 30 30 30 30 61  20 41 6c 70 68 61 2d 49  |0000000a Alpha-I|
    00000010  6e 6e 6f 54 65 63 20 47  6d 62 48 0a 00 00 00 00  |nnoTec GmbH.....|
    00000020  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
    00000030  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
    00000040  .. .. .. .. 8b .. .. ..  .. .. 51 .. .. .. .. ..  |8%...M;...QR.@3.|
    00000050  3b .. .. .. .. .. 60 ..  .. .. .. .. d2 .. .. ..  |;.V"s>`3Lw......|
    00000060  .. .. d8 .. .. .. .. ..  8d .. .. .. .. .. 80 ..  |%....|.[.}.....G|
    00000070  .. .. .. .. 3d .. .. ..  .. .. 7c .. .. .. .. ..  |....=...L.|/..Q}|
    etc.

lastly, create a hash of the 64-byte 0-padded serial number:

> printf "9A0EFE9E" | dd bs=64 count=1 conv=sync 2>/dev/null | openssl dgst -sha512 -binary | hexdump -C

    00000000  4d 52 8c 33 7f c3 7d 47  04 2f fe 05 f8 e6 57 8d  |MR.3..}G./....W.|
    00000010  b8 db f3 98 d5 e3 eb 87  23 d5 ac 5b de cf 55 84  |........#..[..U.|
    00000020  47 05 53 39 7f 89 14 70  25 7a e8 b0 55 70 e5 59  |G.S9...p%z..Up.Y|
    00000030  22 8f 6b 35 12 a3 e6 2a  c0 d6 e8 94 f2 f0 b6 86  |".k5...*........|
    00000040

and compare each hash byte to every 6th byte of the dongle file starting from **0x45**:

    00000000  30 30 30 30 30 30 30 61  20 41 6c 70 68 61 2d 49  |0000000a Alpha-I|
    00000010  6e 6e 6f 54 65 63 20 47  6d 62 48 0a 00 00 00 00  |nnoTec GmbH.....|
    00000020  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
    00000030  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
    00000040  .. .. .. .. .. 4d .. ..  .. .. .. 52 .. .. .. ..  |8%...M;...QR.@3.|
    00000050  .. 8c .. .. .. .. .. 33  .. .. .. .. .. 7f .. ..  |;.V"s>`3Lw......|
    00000060  .. .. .. c3 .. .. .. ..  .. 7d .. .. .. .. .. 47  |%....|.[.}.....G|
    00000070  .. .. .. .. .. 04 .. ..  .. .. .. 2f .. .. .. ..  |....=...L.|/..Q}|

That's it! If all these bytes match, you can buy this dongle and be confident it's in working condition and you are not being scammed. Congratulations.


### Disclaimer

This information is provided solely for educational purposes. No representations or warranties are made regarding its accuracy, completeness, or suitability for any purpose. Use of this information is at your own risk and may result in damage to equipment.
