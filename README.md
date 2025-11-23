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
### Part 1: Flash Dumping
Reverse engineering starts with a flash dump from the router's ROM, so one should do that first. With good eye sight (and double checking on Google) it is clear that that the router's flash chip is the `EN25Q32B`, A.K.A. a series 25 chip one can extract from with a `CH341A` programmer.

Use the male pins on the programmer's included converter board to connect to the test clip, aligning the pink cable with pin 1 and pin 1 with what the product listing's diagram shows.

Clasp the clip on the chip with the pink cable corresponding with the dimpled corner on the IC.

Finally, connect the programmer to a computer's USB port. In the terminal, and in the directory where you want the flash to dump, run `flashrom -c EN25QH32 -p ch341a_spi -r tp_link_wr841n_ext.bin` (*C*hipname, *P*rogrammer name, *R*ead flash/save to).

Extract the flash dump so it is more reverse-engineering friendly for later with `binwalk -e tp_link_wr841n_ext.bin` (*E*xtract).

Disconnect the CH341A programmer from the PC, then disconnect the router from the PC.

### Part 2: UART Logging
Via ethernet cable, connect a PC to one of the router's LAN ports. The router has clearly labeled UART through-holes, but no header pins; solder header pins on before making the UART connections or, desperately and solderlessly, forcefully angle four header pins in the through-holes with an elastic band. Connect the router's GND, Rx, and Tx ports to the GND, Tx, and Rx pins of the UART adapter, respectively.

In a terminal, run `ls -l /dev/serial/by-id` to reveal the `ttyUSBx` port that the CP2102 is on. Run `sudo screen /dev/ttyUSBx 115200` to see the router's UART logs at its baud rate of 115200 (this is the most common UART baud rate around, so finding it was an educated guess).

Plug the router into a power source. Let the UART logs print for a few seconds before pausing them by hitting `Ctrl+A` followed by `[`. Take note of how data is partitioned (for instance, what's the `kernel` range of where data is stored, what's the `config` range, etc) and then hit `q`.

In Kali's Firefox search bar, log into the router using the common IP address of `192.168.0.1`. To crack the SSID and password, see what the terminal logging `screen` reveals once they are set up.

Navigate to the `Quick Setup` tab, setting up the `Operation Mode` as `Access Point` and making the `Wireless Network Name`/`Wireless Password` whatever one likes. Unless one uses this router outside of this exercise, consider keeping the defaults to stay consistent with what's already on the flash dump.

Continue with the rest of `Quick Setup` as default values, click `Finish`, and immediately get ready to hit `Ctrl+A` and then `[` in the terminal after seeing lines about erases and writes.

After hitting `Ctrl+A` and `[`, one can see that the write and erase points of `0x3E0000` and `0x3F0000` set the bounds of where the SSID/password are written in the firmware. As noted earlier, this is exactly the `config` range of the router, so this is *the* flash dump region to focus on.

In a separate terminal window, carve out the RE-friendly file accordingly: `dd if=tp_link_wr841n_ext.bin bs=1 skip=4063232 count=65536 of=config.bin` (*I*n *F*ile, *B*lock *S*ize, *Skip* to 0x3E0000 in decimal, *Count* is the size between 0x3E0000 and 0x3F0000 in decimal, *O*utput *F*ile).

Find a place to start RE-ing from by toying around in the router's web portal. Ideally, find something that relates to passwords or, at a lower-level, the `config` region. One successful approach is to go to `System Tools` > `Backup & Restore`, as backing up one's data yields helpful logs on `screen`. Clicking `Backup` downloads a `conf.bin` file, which is a nod to being in the right place for `config` related matters.

However, the download does not log anything new to the `screen` terminal. So, try `Restore`-ing by uploading a `conf.bin` file, especially one with an invalid format. Create the file in a new terminal with `echo "Hello World" > conf_0.bin` and upload `conf_0.bin` to the web portal. The `screen` terminal yields multiple errors; the `rsl_sys_restoreCfg` error mentioning a `check fail` sounds especially insightful.

### Part 3: Reverse Engineering
To find what file of the extracted .bin has the `rsl_sys_restoreCfg` error, try CD-ing down the tamest looking folder path: it should look something like `cd ~/tp_ink_wr841n/firmware/_tp_link_r841n_ext.bin.extracted/squashfs-root`.

Then search each folder for the error using `strings -f * | grep "rsl_sys_restoreCfg"`. One will only get a hit in the `lib` folder for a file called `libcmm.so`. With `libcmm.so` open in Ghidra, go to `Search > Program Text`, search for `rsl_sys_restoreCfg`, and click `Search All` to find the function's location.

Open the result in Ghidra's Decompiler window and scroll down to the error description (`Config file MD5 check fail`).
Here, it shows if one surpasses that error-checking `if` statement, the restore process would have done an uncompression (`cen_uncompressBuff`), a restoration (`dm_restoreCfg`), and then an `oal_sys_writeCfgFlash`. This is an encryption or decryption process.

Double-click the `oal_sys_writeCfgFlash` function to see that TP-Link specifically does an AES decryption: `aes_cbc_encrypt_intface_bypart`. This requires a `Key` and `IV`, and since they are mentioned on the same line, let's get cracking!

### Part 4: Credential Decrypting
The site `cyberchef.io` is one popular tool for decrypting data. Once there, load an `AES Decrypt` step into a recipe.

For the `Key` field, double-click `gKey` in Ghidra's decompiler which reveals the key in gray text in the `Listing` window. Paste it into the recipe. Repeat the above step for Cyber Chef's `IV` using what TP-Link calls `gIv`.

For CyberChef's `Input Panel`, `conf.bin` needs to be in raw hex. In a new terminal, navigate to the folder with `conf.bin` and run a hex dump tool: `xxd -p config.bin > config.txt` (Output *plain* continuous hex). Run `mousepad config.txt` to easily copy the contents, then paste them into CyberChef's panel.

After observing the `Invalid key length: 8 bytes` error in CyberChef, clarify that `gKey` and `gIv` are formatted in `UTF8`. Presented with a mostly-legible output, hit `Ctrl+F` to search for `SSID val=` and `PreSharedKey val=`. The queries finally reveal the credentials from the flash dump, which are `TP-Link_D052` & `17614627` or something similar. An earlier screenshot confirms this is what the SSID and password were set to, proving we found the right credential pair.


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
