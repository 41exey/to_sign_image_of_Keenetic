# To sign image of ZyXEL Keenetic Lite III rev. A

[![Social](https://img.shields.io/badge/social-telegram-lightgray.svg)](https://teleg.run/c1ewd)
[![Donations](https://img.shields.io/badge/donations-Liberapay-green.svg)](https://liberapay.com/c1ewd/donate)

In Keenetic situation I was going against the grain. I was discovering the OpenWRT build rules and I found zyimage.c. That's the utility that signing all Keenetic images. The source code of this utility opened to me where located device ID needs for sign:

```
  struct signature
  {
    const char magic[4];
    unsigned int device_id;
    char firmware_version[48];
    unsigned int crc32;
  }
```

The start of this c structure located at offset 0x500000 my stock firmware:

```
Offset      0  1  2  3  4  5  6  7   8  9  A  B  C  D  E  F

00500000   5A 4E 42 47 02 13 20 00  5A 79 58 45 4C 20 4B 65   ZNBG    ZyXEL Ke
00500010   65 6E 65 74 69 63 20 4C  69 74 65 20 49 49 49 20   enetic Lite III 
00500020   76 32 2E 30 35 28 41 41  55 51 2E 30 29 41 30 2D   v2.05(AAUQ.0)A0-
00500030   35 36 37 2D 37 37 61 00  4B 31 21 A3               567-77a K1!£
```

Worth remembering about little endian and int type is 4 bytes. So, device ID is 2102018.

Finally, a build rule below:

```
define Device/zyxel_keenetic-lite-iii-a
  SOC := mt7620n
  IMAGE_SIZE := 7872k
  DEVICE_VENDOR := ZyXEL
  DEVICE_MODEL := Keenetic Lite III
  DEVICE_VARIANT := A
  IMAGES += factory.bin
  IMAGE/factory.bin := $$(sysupgrade_bin) | pad-to 64k | check-size | \
		zyimage -d 2102018 -v "ZyXEL Keenetic Lite III"
endef
TARGET_DEVICES += zyxel_keenetic-lite-iii-a
```

## Donations (Optional)

[![Liberapay](https://liberapay.com/assets/widgets/donate.svg)](https://liberapay.com/c1ewd/donate)

