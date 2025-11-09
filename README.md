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

First, we look to the WR841N's documented pinout to connect a CH341A flash programmer to the router's flash ROM to read all its contents. Then it was extracted via the following command: `flashrom -p ch341a_spi -r tp_link_wr841n_ext.bin`. Critically, the flash dump's boot partition reveals that a config section exists from 0x3E0000 to 0x3F0000. So, running `binwalk -E tp_link_wr841n_ext.bin` (where `-E` is for entropy) reveals an entropy plot with anomalies around the 0x3E0000th(4,063,232th in decimal) offset.

Let's carve out the config section into `config.bin` using Data Duplicator: `dd if=tp_link_wr841n_ext.bin bs=1 skip=4063232 count=65536 of=config.bin`. The `if` stands for "in file", `bs` means block size which is 1 for easier math, the `skip` ahead point explains itself, and `count` is the decimal window size calculated from 0x3F0000 minus 0x3E0000. The garbled, resulting file must then be decrypted in Ghidra. 
