# appl_param1 modification

If you are lucky enough not to have updated the Luxtronik firmware to V4.81.**3**, you can still gain SSH access to the controller thanks to  [CVE-2024-22894](https://github.com/Jaarden/CVE-2024-22894).

This can be used to equalize the compressor start counters of compressors 1 and 2 after patching the compressor heater bug. Once fixed, the Luxtronik controller will reliably start the compressor with the lower start count. However, if the counters have drifted too far apart due to the aforementioned bug, rebalancing may otherwise take a year or more.

### About appl_param1:
In the controller's home directory, you will find a file named `appl_param`. You can ignore it. Although it contains the application parameters, as its name suggests, it is never read from.

I can only speculate that one stormy November night, a Luxtronik controller must have read a corrupted `appl_param` file and bricked the system. This must have come to the attention of a developer, who then embarked on a side-quest to add a CRC16 checksum to the `appl_param` file.

Thus `appl_param1` was born: 2 bytes larger than it's older, unchecked sibling.

The side-quest must have offered a very poor reward, because the developer simply placed the cursor on the last line of the old code, pressed enter and started writing new code. Since that fateful moment, more than one parameter file has existed.

`appl_param1` rolls over into `appl_param2` as a backup on each parameter change, paying hommage to the word "Twin" in the system's name.

### Steps

Make note of the modification date of `appl_param1`. Param files are periodically rewritten. Make sure heat pump stays idle.

- SSH into the controller
- Verify the compressor start counters at offsets 0x1516 and 0x151e:

> \# hexdump -C -s $((0x1510)) -n 32 appl_param1
> 00001510  00 00 01 00 00 00 a4 2c  00 00 01 00 00 00 64 28  |.......,......d(|
> 00001520  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|

- Copy the file to your development machine:

> $ scp -O -oKexAlgorithms=+diffie-hellman-group1-sha1 -o HostKeyAlgorithms=+ssh-rsa -c aes128-cbc root@192.168.1.123:/home/appl_param1 .

- Open the file in your hex editor of choice
- Cut the first two bytes (CRC)
- Save the modified file as `appl_param1_bare` (will be 2 bytes smaller)

Run CRC16 on `appl_param1_bare`:

    $ python3 - <<'PY'
    import sys
    POLY = 0xA001
    crc = 0xFFFF
    with open("appl_param1_bare", "rb") as f:
        while (b := f.read(1)):
        crc ^= b[0]
            for _ in range(8):
            if crc & 1:
                crc = (crc >> 1) ^ POLY
            else:
                crc >>= 1

    print(f"{crc:04x}")
    PY

- Verify that this CRC16 implementation produces the same checksum as in the original file (little-endian).
- Modify the compressor start counter bytes in appl_param_bare.
- Recalculate the CRC.
- SSH back into controller and modify file directly on the device (make a backup first):

Apply modifications:
> \# printf '\xa4\x2c' | dd of=appl_param1 bs=1 count=2 seek=$((0x151e)) conv=notrunc 
> \# printf '\x??\x??' | dd of=appl_param1 bs=1 count=2 seek=0 conv=notrunc 

Re-verify:
> \# hexdump -C -s $((0x1510)) -n 32 appl_param1
> \# hexdump -C -s 0 -n 32 appl_param1

Power off the Luxtronik controller, wait 1 minute, then power it on again.

This information is provided solely for educational purposes. No representations or warranties are made regarding its accuracy, completeness, or suitability for any purpose. Use of this information is at your own risk and may result in damage to equipment.
