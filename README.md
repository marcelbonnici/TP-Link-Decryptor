## TP-Link Decryptor - Router Hardware Hacking
This hardware hacking project describes how to exploit the widespread WR841N router to uncover its network name and password. The process is accomplished using a CP2102 TTL to USB Adapter, USB Logic Analyzer, male through-hole header pins (to connect to the router's UART pins), and a variety of software to interpret the device's messages.

### Motivation
As a software engineer in cybersecurity, I joined hackathons where my colleagues’ reverse-engineering skills humbled me. With a robotics background spanning mechanical, electrical, and software engineering, I enjoyed finding clever ways to hack sensors. But watching my peers move through Ghidra like their childhood neighborhood made me realize how much deeper the IoT rabbithole goes, inspiring me to complete an *ethical hacking* project solo.

### TODO
Make hardware hacking thumbnail, router hacking video

### Video Demonstration
[CLICK HERE TO WATCH](https://youtu.be/NpEaa2P7qZI)

[![TP-Link Decryptor](https://i.ytimg.com/vi/NpEaa2P7qZI/maxresdefault.jpg)](https://youtu.be/NpEaa2P7qZI)

### Hardware
* [TP-Link WR841n Wireless Router](https://www.ebay.com/sch/i.html?_nkw=WR841N&_sacat=0&_from=R40&_trksid=p4624852.m570.l1313)
* [Phillips Screw Driver](https://www.amazon.com/Insulated-Phillips-Screwdriver-Magnetic-PH2100MM/dp/B094JL6J3D/ref=sr_1_4?crid=26OP3UZ0JQ4A3&dib=eyJ2IjoiMSJ9.kusROkxP7ncQMU_ljupabsuCrXgKtErQgJVNXlBS6jFNVoucSEnKYE8ZsnrGf8eDes4IZnvTt8G0Jkdmixw396HEnAxObGftSv1xPYMxJaJd-HfLTU1I0Y36HlyWiJQi4zzaLuXCgopsZ0T9_FVwWa4u-X5OTUZB17E_02p1YjnifTCCY-xCxhGUHbamXHq6ZbYc7E2zyOmf5ADo41vjD2TK4rNwj-xjJ8j28MDxF40utlCbVrCDc2gu78s0V8BFejZm5c-JX6sZ8F_Y4uYiOOAiosNQFy1cbDuOpUg692o.lzh-rDpCyNdAiqhKPXfXxGnEcLUK_EvmtjpEa9pI-bc&dib_tag=se&keywords=flat%2Bphillips%2Bscrew%2Bdriver&qid=1762617838&refinements=p_36%3A400-&rnid=1243644011&s=hi&sprefix=flat%2Bphillips%2Bscrew%2Bdriver%2Ctools%2C77&sr=1-4&th=1)
* [CP2102 TTL USB to UART Adapter](https://www.amazon.com/WWZMDiB-CP2102-USB-TTL-Programming/dp/B0BCYRFZJD/ref=sr_1_2_sspa?dib=eyJ2IjoiMSJ9.NrP2CPuE_H5AAFYIn62Y6KiXSZCdZyg8VLSA6U1hzoddqG4kDyQjDg1ezazMmhPeVDlf5E7BTW7x4_SoxrVNcviP7Yx7DKwPJrHdcg2s_L-vuubWzCFAD6Oa2ONH5SLueaBMvWZMjtFFquuqdcbJUvko_mXsSr1HShpW9CR-V8bB8uOxDsCn8xPXpGMgjaaVOZnFpmqj1VeVDmmYCX1_E3yvO4KF2Q5w13R61WvBbKo.kY2iTAWP1YoZCJQht7EbmC7SV1gVo9L3a8E-pCeBu9Y&dib_tag=se&keywords=CP2102+TTL+USB+to+UART+Adapter&qid=1762617896&sr=8-2-spons&sp_csd=d2lkZ2V0TmFtZT1zcF9hdGY&psc=1)
* [Male Through-Hole Header Pins](https://www.amazon.com/MCIGICM-Header-2-45mm-Arduino-Connector/dp/B07PKKY8BX/ref=sr_1_2?crid=13Y44L8BT8APZ&dib=eyJ2IjoiMSJ9.EcxqGz3KvIgq4FfcTan94MEe415-CNFjACNxmpJpwnlb3jupU9mQJhZ8C07eBxqc6wm2tL67J1yOB5xPZy7JX7_NQEG8YWnqhXMwvIXvR__HA-rhzWI_cxhCek8CPgF9tDwoY8vQ_k6v1OnQuB8JElJB9s0nsKvGaaQsrqVO9rZt2BJVq1wR8XkkFMA8jyamBGNy7VbTBkc7zDYCF7S_tXPpmbn5SDV6RdLL8ajoo3U.fZmDc4MQNBDSSeBrUKLouTBewVK3eGczMbKsIe2vt1U&dib_tag=se&keywords=male+through+hole+header+pins&qid=1762617988&sprefix=male+through+hole+header+pins%2Caps%2C105&sr=8-2)
* [USB Logic Analyzer](https://www.amazon.com/Comidox-Analyzer-Device-Channel-Arduino/dp/B07KW445DJ/ref=sr_1_5?crid=2K5DQ2ROBIWTO&dib=eyJ2IjoiMSJ9.dlhze1_6QV8KrkIYmy_VnUx2ZPRINkOkCT1PTbDGxHMdGzMUumiE2ZGBKG1xVkXXob0r1gdcwXlsVikGmZFwlehoYil65Jjq9aTyku4TlieWAsq5tNtwSdep9y1KmvjmsZ_xf3lkerw_TX_WUWn92ptBEwKl8JuooYKbXnCK2SX_3tYUMe1p14TsXZRw_EOzD_zbc27gdxPppqaMGnxJ6J2-5T7OAYGnGyPQ8p9FQnU.aDyyepV3-k1uPBjZQC77oXtkapq8Sgc5xNKruKtiQUI&dib_tag=se&keywords=usb+logic+analyzer&qid=1762618073&sprefix=usb+logic+analyzer%2Caps%2C108&sr=8-5)

### Requirements
The software required:
1. [Kali Linux](https://www.kali.org/get-kali/#kali-platforms)
2. [Sigrok Pulseview (for logic captures)](https://sigrok.org/wiki/PulseView)
3. [Binwalk](https://www.kali.org/tools/binwalk/)
4. [Ghidra](https://ghidralite.com/)
5. [Screen](https://linuxize.com/post/how-to-use-linux-screen/)
6. [Flashrom](https://www.flashrom.org/)
7. [DD (Data Duplicater)](https://medium.com/@moraneus/dd-how-this-obscure-linux-command-can-save-or-destroy-your-data-15f4955d90c1)
8. [CyberChef](https://gchq.github.io/CyberChef/)

## How to Expose the SSID & Password

Open Kali VM
We need a flash dump from the ROM, so let's get that out of the way first
With good eye sight--and double checking on Google--we verify that the router's Flash chip is the EN25Q32B, which is a series 25 chip fit for extraction via a `CH341A` programmer
Use the male pins on the programmer's included converter board to connect to the test clip, aligning the pink cable with pin 1 and pin 1 with what the product listing's diagram shows
Clasp the clip on the chip with the pink cable corresponding with the dimpled corner on the IC
Connect the programmer to our computer's USB port
In the terminal, and in the directory where you want the flash to dump, run `flashrom -c EN25QH32 -p ch341a_spi -r tp_link_wr841n_ext.bin` (Chipname, Programmername, Read flash/save to)
Extract the flash dump so it is more reverse-engineering friendly for later with `binwalk -e tp_link_wr841n_ext.bin`
Disconnect the CH341A programmer from the PC and then the router
Connect to PC to router via ethernet on LAN port
Connect GND/Rx/Tx of router to GND/Tx/Rx of UART to USB converter
In command terminal, run `ls -l /dev/serial/by-id` to reveal the `ttyUSBx` port that the CP2102 is on
Run `sudo screen /dev/ttyUSBx 115200` to see the UART logs of the router at the router's baud rate of 115200 (most common UART baud rate around)
Plug router into power source
After several seconds, hit `Ctrl+A`, take note of how data is partitioned (for instance, what's the `kernel` range of where data is stored, what's the `config` range, etc), and then hit `q`
Log into router in the web browser from the very common ip address of 192.168.0.1
To crack the SSID and password, let's see what `screen` reveals when we set one up.
Navigate to the Quick Setup tab, setting up the `Operation Mode` as `Access Point` and setting up the `Wireless Network Name`/`Wireless Password` to whatever you'd like
Continue with the rest of `Quick Setup` as default values, and RIGHT after you click `Finish`, get ready to hit `Ctrl+A` after seeing lines about erases and writes in the `screen` terminal
Hit `Ctrl+A` and then `[`, deducing that the write and erase points of `0x3E0000` and `0x3F0000` set the bounds of where the SSID and password are written in the firmware
We noted earlier that this range is exactly the config section of the router, so we can confidently assume this is the region of the flash dump we need to focus on
Let's carve out our RE-friendly file from earlier to the config range: `dd if=tp_link_wr841n_ext.bin bs=1 skip=4063232 count=65536 of=config.bin` (In File, Block Size, Skip to 0x3E0000 in decimal, Count is the size between 0x3E0000 and 0x3F0000, Output File)
Let's try to find a place to start RE-ing from by toying around in the router's web portal to see what else relates to passwords or, at a lower-level, the `config`
Eventually, I tinkered my way over to `System Tools` > `Backup & Restore`, thinking that backing up my data would yielding helpful logs on `screen`
Clicking `Backup` downloads a `conf.bin` file, which is a nod to being in the right place for `config` related matters. However, the `screen` terminal printed nothing.
So, we try `Restore`-ing by uploading a `conf.bin` file, which shows details on the `screen` side especially when uploading an invalid `.bin` file: `echo "Hello World" > conf_0.bin`
The MVP error message was `rsl_sys_restoreCfg` mentioning an `MD5 check fail`
We want to find what file of the extracted .bin has the message, which we find by CD-ing down the most vanilla folder path (leading us to `squashfs-root`)
Then I grepped around each folder with `strings -f * | grep "rsl_sys_restoreCfg"`, to which I only got a hit in the `lib` folder for a file called `libcmm.so`
With libcmm.so opened in Ghidra, go to `Search > Program Text` to search for `rsl_sys_restoreCfg`, click `Search All` which reveals the function's location that we double-click
Once there, we scroll down to the RHS error description in Ghidra
Then, we see that if we surpass that error-checking if function (Mentioning MD5), the restore process would have done uncompression, then restoring, and then a `oal_sys_writeCfgFlash`
This is an encryption or decryption process!
Double-click the `oal_sys_writeCfgFlash` function to see that we are doing an encryption via `aes_cbc_encrypt_intface_bypart` with 2 stacks called `gKey` and `gIv`
We can use the site `cyberchef.io` to decrypt data, so we now know to load an `AES Decrypt` step to our recipe
For the `Key` field, we double-click `gKey` in Ghidra which reveals the key transcribed in gray and paste it into our recipe
Repeat the above step for the `IV` field and `gIv`
For the `Input Panel`, convert conf.bin into raw hex for CyberChef: `xxd -p config.bin > config.txt`
Run `mousepad config.txt` to easily copy the contents, then paste them into CyberChef's panel
After observing the `Invalid key length`, clarify that `gKey` and `gIv` are formatted as `UTF8` instead of `HEX`
With a mostly-readable output, we try hitting `Ctrl+F` to successfully search for `SSID val` and `PreSharedKey` that were stored for last flash dump, which were clearly TP-Link defaults


Make the key the numeric value we found for `gKey`, the IV the number we found for `gIv`, both those fields should be `UTF8`

We double click on `gKey` first which, in the left panel, shows us an `addr` word and, one the same line, a key of `1528632946736109`. Several lines above that, we see the initialization vector for `gIv` of `1528632946736539`

8) Convert conf.bin into a .txt file to copy the raw hex into cyberchef: `xxd -p config.bin > config.txt` followed by copying all of the .txt file's contents (get contents via `mousepad config.txt`) and going to `cyberchef.io`. Copy it into the `Input Panel` and on the left go to `Encyption / Encoding` drag and drop `AES Decrypt` into the recipe. For `AES Decrypt` fields, make the key the numeric value we found for `gKey`, the IV the number we found for `gIv`, both those fields should be `UTF8` instead of `HEX`, the mode `CBC`, Input of `Hex`, and Output of `Raw`. `Bake` it, then see that it looks like XML, Ctrl+F the webpage and type in `SSID val` to find it is indeed there and then Ctrl+F for `PreSharedKey` to find the password.









First, we look to the WR841N's documented pinout to connect a CH341A flash programmer to the router's flash ROM to read all its contents. In the terminal, CD into Then it was extracted via the following command: `flashrom -c ??? -p ch341a_spi -r tp_link_wr841n_ext.bin`. Critically, the flash dump's boot partition reveals that a config section exists from 0x3E0000 to 0x3F0000. So, running `binwalk -E tp_link_wr841n_ext.bin` (where `-E` is for entropy) reveals an entropy plot with anomalies around the 0x3E0000th(4,063,232th in decimal) offset. Next, running `binwalk -e tp_link_wr841n_ext.bin` creates the `_tp_link_wr841n_ext.bin.extracted` that we need for later

Let's carve out the config section into `config.bin` using Data Duplicator: `dd if=tp_link_wr841n_ext.bin bs=1 skip=4063232 count=65536 of=config.bin`. The `if` stands for "in file", `bs` means block size which is 1 for easier math, the `skip` ahead point explains itself, and `count` is the decimal window size calculated from 0x3F0000 minus 0x3E0000. The garbled, resulting file must then be decrypted in Ghidra. 


TBD

But before then, we make sure that we are connected to the router via ethernet on a LAN port, logged into the web portal, have the UART connection plugged into our computer via USB, and then run `sudo screen /dev/ttyUSB0 115200` (115200 is an educated guess since 115200 is the baud rate for most UART devices) based on what USB port it's in. In the web portal, there's a System Tools > Backup & Restore tab that lets us run that process (downloading a conf.bin file to our computer) which should reveal how encryption and decryption is done. So, we download the conf.bin file and then upload it followed by hitting 'Restore' to which the `sudo screen` terminal should show things happening during the restore/upload process. Once its rebooting, hit Ctrl+A+[ to enter Copy Mode to look over everything at our own speed. However, after trying to look through we can't find anything instrumental, so we log back into the web portal (should log us out after restore completes) and try to find a way to get the config to fail and see if that leads to us getting any messaging back.

4) In the terminal, go to `cd ~/tp_link_wr841n/configs` and then do `echo "00000000000000000000000000000" > conf_0.bin` to then check that `cat conf_0.bin` just returns all the zeros sent to that file.

5) Upload that file to the restore section of the web portal, hoping it reveals an error on the sudo screen since it is not a valid backup file. Thus, we can trace back its failure message in a way that we can trace back and RE.

6) Doing so reveals 2 error messages: one named `rsl_sys_restoreCfg` and another named `rdp_restoreCfg`. We want to find what file of the extracted bin has the first error message, which we find by going to the `lib` via `cd firmware/_tp_link_wr841n_ext.bin.extracted/squashfs-root/lib` and then running `strings -f * | grep "rsl_sys_restoreCfg"` which reveals that we only get a hit for that error name in a file called `libcmm.so`. With libcmm.so opened in Ghidra (`sudo apt install ghidra`, then find `ghidra` in your system's search engine and open that, and follow video 8.2 from there), go to Search > Program Text to `Search for:` `rsl_sys_restoreCfg` then click `Search All` which reveals the function's location of 2c6cc which we're gonna go to. Once there, we scroll down to the error description in Ghidra (wich we already saw when `sudo screen`ing) and see that if we surpass that error-checking if function (Mentioning MD5), the restore process would have done uncompression steps, then restoring of the config, and then eventually see a `oal_sys_writeCfgFlash` function as our last function indicative of an encryption or decryption process.

7) We hop to the `oal_sys_writeCfgFlash` function in Ghidra by clicking it, which shows us that we are doing an encryption via an `aes_cbc_encrypt_intface_bypart` function call preceded by 2 memcpy's into the stack which are named `gKey` (data location) and `giv` (initialization vector), respectively. We double click on `gKey` first which, in the left panel, shows us an `addr` word and, one the same line, a key of `1528632946736109`. Several lines above that, we see the initialization vector for `gIv` of `1528632946736539`

8) Convert conf.bin into a .txt file to copy the raw hex into cyberchef: `xxd -p config.bin > config.txt` followed by copying all of the .txt file's contents (get contents via `mousepad config.txt`) and going to `cyberchef.io`. Copy it into the `Input Panel` and on the left go to `Encyption / Encoding` drag and drop `AES Decrypt` into the recipe. For `AES Decrypt` fields, make the key the numeric value we found for `gKey`, the IV the number we found for `gIv`, both those fields should be `UTF8` instead of `HEX`, the mode `CBC`, Input of `Hex`, and Output of `Raw`. `Bake` it, then see that it looks like XML, Ctrl+F the webpage and type in `SSID val` to find it is indeed there and then Ctrl+F for `PreSharedKey` to find the password.
