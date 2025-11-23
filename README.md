## TP-Link Decryptor - Router Hardware Hacking
This hardware hacking project describes how to exploit the widespread WR841N router to uncover its network name and password. The process is accomplished using a CP2102 TTL to USB Adapter, USB Logic Analyzer, male through-hole header pins (to connect to the router's UART pins), and a variety of software to interpret the device's messages.

### Motivation
As a software engineer in cybersecurity, I joined hackathons where my colleagues’ reverse-engineering skills humbled me. With a robotics background spanning mechanical, electrical, and software engineering, I enjoyed finding clever ways to hack sensors. But watching my peers move through Ghidra like their childhood neighborhood made me realize how much deeper the IoT rabbit hole goes, inspiring me to complete an *ethical hacking* project solo.

### TODO
Make hardware hacking thumbnail, router hacking video

### Video Demonstration
[CLICK HERE TO WATCH](https://youtu.be/NpEaa2P7qZI)

[![TP-Link Decryptor](https://i.ytimg.com/vi/NpEaa2P7qZI/maxresdefault.jpg)](https://youtu.be/NpEaa2P7qZI)

### Hardware
* [TP-Link WR841n Wireless Router](https://www.ebay.com/sch/i.html?_nkw=WR841N&_sacat=0&_from=R40&_trksid=p4624852.m570.l1313)
* [Phillips Screw Driver](https://www.amazon.com/Insulated-Phillips-Screwdriver-Magnetic-PH2100MM/dp/B094JL6J3D/ref=sr_1_4?crid=26OP3UZ0JQ4A3&dib=eyJ2IjoiMSJ9.kusROkxP7ncQMU_ljupabsuCrXgKtErQgJVNXlBS6jFNVoucSEnKYE8ZsnrGf8eDes4IZnvTt8G0Jkdmixw396HEnAxObGftSv1xPYMxJaJd-HfLTU1I0Y36HlyWiJQi4zzaLuXCgopsZ0T9_FVwWa4u-X5OTUZB17E_02p1YjnifTCCY-xCxhGUHbamXHq6ZbYc7E2zyOmf5ADo41vjD2TK4rNwj-xjJ8j28MDxF40utlCbVrCDc2gu78s0V8BFejZm5c-JX6sZ8F_Y4uYiOOAiosNQFy1cbDuOpUg692o.lzh-rDpCyNdAiqhKPXfXxGnEcLUK_EvmtjpEa9pI-bc&dib_tag=se&keywords=flat%2Bphillips%2Bscrew%2Bdriver&qid=1762617838&refinements=p_36%3A400-&rnid=1243644011&s=hi&sprefix=flat%2Bphillips%2Bscrew%2Bdriver%2Ctools%2C77&sr=1-4&th=1)
* [CP2102 TTL USB to UART Adapter](https://www.amazon.com/WWZMDiB-CP2102-USB-TTL-Programming/dp/B0BCYRFZJD/ref=sr_1_2_sspa?dib=eyJ2IjoiMSJ9.NrP2CPuE_H5AAFYIn62Y6KiXSZCdZyg8VLSA6U1hzoddqG4kDyQjDg1ezazMmhPeVDlf5E7BTW7x4_SoxrVNcviP7Yx7DKwPJrHdcg2s_L-vuubWzCFAD6Oa2ONH5SLueaBMvWZMjtFFquuqdcbJUvko_mXsSr1HShpW9CR-V8bB8uOxDsCn8xPXpGMgjaaVOZnFpmqj1VeVDmmYCX1_E3yvO4KF2Q5w13R61WvBbKo.kY2iTAWP1YoZCJQht7EbmC7SV1gVo9L3a8E-pCeBu9Y&dib_tag=se&keywords=CP2102+TTL+USB+to+UART+Adapter&qid=1762617896&sr=8-2-spons&sp_csd=d2lkZ2V0TmFtZT1zcF9hdGY&psc=1)
* [Male Through-Hole Header Pins](https://www.amazon.com/MCIGICM-Header-2-45mm-Arduino-Connector/dp/B07PKKY8BX/)
* [Flash ROM Programmer (CH341A)](https://www.amazon.com/Geekstory-CH341A-EEPROM-Programmer-Module/dp/B098DYJ3LQ)

### Requirements
The software required:
1. [Kali Linux](https://www.kali.org/get-kali/#kali-platforms)
2. [Ghidra](https://ghidralite.com/)
3. [Binwalk](https://www.kali.org/tools/binwalk/)
4. [Ghidra](https://ghidralite.com/)
5. [Screen](https://linuxize.com/post/how-to-use-linux-screen/)
6. [Flashrom](https://www.flashrom.org/)
7. [DD (Data Duplicater)](https://medium.com/@moraneus/dd-how-this-obscure-linux-command-can-save-or-destroy-your-data-15f4955d90c1)
8. [CyberChef](https://gchq.github.io/CyberChef/)

## How to Expose the SSID & Password
### Part 1: Opening The Router
Place the router upside down on a table. Unscrew the two screws near the back legs of the router. Place the router right side up to start removing the router's lid.

![Unscrewing The Router](https://3o6pkajl5f.ucarecd.net/245ba879-f372-40bb-80a7-e47fad27a465/1a.JPG)

Using a flat head screwdriver (or other prying device), wedge it between the lid and base starting by the antenna near the `RESET` button. Twist your tool until the lid releases, making sure to not dig the tool any deeper into the router than needed. Moving around the router clockwise, pry open the remaining 3 corners until the lid fully disconnects. Remove it to see the PCB.

![Opening The Router](https://3o6pkajl5f.ucarecd.net/ecb5e890-6697-4cc7-a883-321ba9a040c1/1b.JPG)

### Part 2: Flash Dumping
Reverse engineering starts with a flash dump from the router's ROM, so one should do that first. With good eye sight (and double checking on Google) it is clear that that the router's flash chip is the `EN25Q32B`, A.K.A. a series 25 chip one can extract from with a `CH341A` programmer.

![Identifying the Flash Chip](https://3o6pkajl5f.ucarecd.net/883fa73c-a2ee-4aa4-871e-e2563c459b38/2a.jpg)

Use the male pins on the programmer's included converter board to connect to the test clip, aligning the pink cable with pin 1 and pin 1 with what the product listing's diagram shows.

![CH341A Setup](https://3o6pkajl5f.ucarecd.net/3516ea91-7cc8-430b-9469-62d295f61720/2b.jpg)

Clasp the clip on the chip with the pink cable corresponding with the dimpled corner on the IC.

![Clamping Onto The Flash Chip](https://3o6pkajl5f.ucarecd.net/2f1dcd34-3199-4070-a62b-66eaa927dd33/2c.gif)

Finally, connect the programmer to a computer's USB port. In the terminal, and in the directory where you want the flash to dump, run `flashrom -c EN25QH32 -p ch341a_spi -r tp_link_wr841n_ext.bin` (*C*hipname, *P*rogrammer name, *R*ead flash/save to).

Extract the flash dump so it is more reverse-engineering friendly for later with `binwalk -e tp_link_wr841n_ext.bin` (*E*xtract).

![Flashing and Binwalking](https://3o6pkajl5f.ucarecd.net/76f32d44-8d62-46f4-8ce8-c5209cdc45c4/2d.gif)

Disconnect the CH341A programmer from the PC, then disconnect the router from the PC.

### Part 3: UART Logging
Via ethernet cable, connect a PC to one of the router's LAN ports. The router has clearly labeled UART through-holes, but no header pins; solder header pins on before making the UART connections or, desperately and solderlessly, forcefully angle four header pins in the through-holes with an elastic band. Connect the router's GND, Rx, and Tx ports to the GND, Tx, and Rx pins of the UART adapter, respectively.

![UART Connections](https://3o6pkajl5f.ucarecd.net/899086df-e08b-4d6f-b49c-fa10269dec1d/3a.jpg)

In a terminal, run `ls -l /dev/serial/by-id` to reveal the `ttyUSBx` port that the CP2102 is on. Run `sudo screen /dev/ttyUSBx 115200` to see the router's UART logs at its baud rate of 115200 (this is the most common UART baud rate around, so finding it was an educated guess).

Plug the router into a power source. Let the UART logs print for a few seconds before pausing them by hitting `Ctrl+A` followed by `[`. Take note of how data is partitioned (for instance, what's the `kernel` range of where data is stored, what's the `config` range, etc) and then hit `q`.

![Observing Partitions in the Boot Logs](https://3o6pkajl5f.ucarecd.net/7d7b0cac-e28f-41a3-b1d0-2f8c04303676/3b.gif)

In Kali's Firefox search bar, log into the router using the common IP address of `192.168.0.1`. To start cracking the SSID and password, see what the terminal logging `screen` reveals once they are set up.

Navigate to the `Quick Setup` tab, setting up the `Operation Mode` as `Access Point` and making the `Wireless Network Name`/`Wireless Password` whatever one likes. Unless one uses this router outside of this exercise, consider keeping the defaults to stay consistent with what's already on the flash dump.

![Diving into the Router's Web Portal](https://3o6pkajl5f.ucarecd.net/cf6dda56-32dd-48f8-a784-9397cbec3c14/3c.gif)

Continue with the rest of `Quick Setup` as default values, click `Finish`, and immediately get ready to hit `Ctrl+A` and then `[` in the terminal after seeing lines about erases and writes.

After hitting `Ctrl+A` and `[`, one can see that the write and erase points of `0x3E0000` and `0x3F0000` set the bounds of where the SSID/password are written in the firmware. As noted earlier, this is exactly the `config` range of the router, so this is *the* flash dump region to focus on.

[Figure 8: Write & erase]

In a separate terminal window, carve out the RE-friendly file accordingly: `dd if=tp_link_wr841n_ext.bin bs=1 skip=4063232 count=65536 of=config.bin` (*I*n *F*ile, *B*lock *S*ize, *Skip* to 0x3E0000 in decimal, *Count* is the size between 0x3E0000 and 0x3F0000 in decimal, *O*utput *F*ile).

Find a place to start RE-ing from by toying around in the router's web portal. Ideally, find something that relates to passwords or, at a lower-level, the `config` region. One successful approach is to go to `System Tools` > `Backup & Restore`, as backing up one's data yields helpful logs on `screen`. Clicking `Backup` downloads a `conf.bin` file, which is a nod to being in the right place for `config` related matters.

[Figure 9: Backup]

However, the download does not log anything new to the `screen` terminal. So, try `Restore`-ing by uploading a `conf.bin` file, especially one with an invalid format. Create the file in a new terminal with `echo "Hello World" > conf_0.bin` and upload `conf_0.bin` to the web portal. The `screen` terminal yields multiple errors; the `rsl_sys_restoreCfg` error mentioning a `check fail` sounds especially insightful.

[Figure 10: rsl_sys_restoreCfg GIF?]

### Part 4: Reverse Engineering
To find what file of the extracted .bin has the `rsl_sys_restoreCfg` error, try CD-ing down the tamest looking folder path: it should look something like `cd ~/tp_ink_wr841n/firmware/_tp_link_r841n_ext.bin.extracted/squashfs-root`.

Then search each folder for the error using `strings -f * | grep "rsl_sys_restoreCfg"`. One will only get a hit in the `lib` folder for a file called `libcmm.so`. With `libcmm.so` open in Ghidra, go to `Search > Program Text`, search for `rsl_sys_restoreCfg`, and click `Search All` to find the function's location.

Open the result in Ghidra's Decompiler window and scroll down to the error description (`Config file MD5 check fail`).
Here, it shows if one surpasses that error-checking `if` statement, the restore process would have done an uncompression (`cen_uncompressBuff`), a restoration (`dm_restoreCfg`), and then an `oal_sys_writeCfgFlash`. This is an encryption or decryption process.

[Figure 11: Decompiler GIF]

Double-click the `oal_sys_writeCfgFlash` function to see that TP-Link specifically does an AES decryption: `aes_cbc_encrypt_intface_bypart`. This requires a `Key` and `IV`, and since they are mentioned on the same line, let's get cracking!

[Figure 12: oal_sys_writeCfgFlash GIF]

### Part 5: Credential Decrypting
The site `cyberchef.io` is one popular tool for decrypting data. Once there, load an `AES Decrypt` step into a recipe.

[Figure 13: CyChef AES]

For the `Key` field, double-click `gKey` in Ghidra's decompiler which reveals the key in gray text in the `Listing` window. Paste it into the recipe. Repeat the above step for Cyber Chef's `IV` using what TP-Link calls `gIv`.

[Figure 14: gKey GIF]

For CyberChef's `Input Panel`, `conf.bin` needs to be in raw hex. In a new terminal, navigate to the folder with `conf.bin` and run a hex dump tool: `xxd -p config.bin > config.txt` (Output *plain* continuous hex). Run `mousepad config.txt` to easily copy the contents, then paste them into CyberChef's panel.

After observing the `Invalid key length: 8 bytes` error in CyberChef, clarify that `gKey` and `gIv` are formatted in `UTF8`. Presented with a mostly-legible output, hit `Ctrl+F` to search for `SSID val=` and `PreSharedKey val=`.

[Figure 15: Ctrl+F GIF]

The queries finally reveals the credentials from the flash dump, which are `TP-Link_D052` & `17614627` or something similar. An earlier screenshot confirms this is what the SSID and password were set to, proving we found the right credential pair.

## Future Possibilities
Creating a script that ingests a binary for this router and automatically returns the SSID & password would make this walkthrough more user-friendly. It would also give me experience with Ghidra's API, enriching my reverse engineering skillset. Until then, I look forward to astonishing my colleagues at our next hackathon.
