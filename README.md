### Forensics 01
#### Check this file!!
We managed to get a picture of the crime scene: [CTF.jpg](CTF.jpg)<br />
```bash
$ file CTF.jpg
CTF.jpg: JPEG image data, JFIF standard 1.01, aspect ratio, density 72x72, segment length 16, Exif Standard: [TIFF image data, big-endian, direntries=10, description=RUX{abi3ahG1Mae9dieeWooLah1e}, manufacturer=Apple, model=iPhone 5, orientation=upper-left, xresolution=180, yresolution=188, resolutionunit=2, software=Photos 1.1, datetime=2015:10:24 08:17:21], baseline, precision 8, 2448x3264, frames 3
```

Flag is **RUX{abi3ahG1Mae9dieeWooLah1e}**

### Forensics 02
##### man rm
Man, i just rm'd this :/ [fs](fs)
```bash
$ file fs
fs: Linux rev 1.0 ext2 filesystem data, UUID=7e46643c-aae9-4323-bcb9-aa2f6056cebd
$ mount -o loop,ro fs /mnt/
$ find /mnt/
/mnt/
/mnt/lost+found
/mnt/key.doc
$ cat /mnt/key.doc
Move along. Nothing to see here.
$ umount /mnt/
$ binwalk -e fs 
DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             Linux EXT filesystem, rev 1.0 ext2 filesystem data, UUID=7e46643c-aae9-4323-bcb9-aa2f60566056
30208         0x7600          bzip2 compressed data, block size = 900k
$ find
.
./fs
./_fs.extracted
./_fs.extracted/7600
$ file ./_fs.extracted/7600
./_fs.extracted/7600: POSIX tar archive (GNU)
$ tar xvf ./_fs.extracted/7600
look_inside.tbz
$ file look_inside.tbz
look_inside.tbz: gzip compressed data, from Unix, last modified: Sun Oct 18 13:35:53 2015
$ tar xvf look_inside.tbz 
look_inside/
look_inside/key.txt
$ cat look_inside/key.txt
RUX{dce3ae00-6ff9-4dc5-a8ca-ce717d247694}

```
Flag is **RUX{dce3ae00-6ff9-4dc5-a8ca-ce717d247694}**

### Forensics 03
#### corruption reigns
What's wrong with this file?? [corrupted.data](corrupted.data)

```bash
$ file corrupted.data 
corrupted.data: data
$ strings corrupted.data 
files/XX
!SGl
files/the_file
n-=B
S*.)
^9WXX
files/XX
!SGl
files/the_fileXX
$ hexdump -C corrupted.data | head
00000000  58 58 03 04 14 03 00 00  00 00 b1 21 53 47 00 00  |XX.........!SG..|
00000010  00 00 00 00 00 00 00 00  00 00 06 00 00 00 66 69  |..............fi|
00000020  6c 65 73 2f 58 58 03 04  14 03 00 00 08 00 ad 21  |les/XX.........!|
00000030  53 47 6c 99 b7 7c a6 00  00 00 bd 01 00 00 0e 00  |SGl..|..........|
00000040  00 00 66 69 6c 65 73 2f  74 68 65 5f 66 69 6c 65  |..files/the_file|
00000050  a5 d1 bf 0e 82 30 10 c7  f1 9d a7 e8 e6 d4 e4 a0  |.....0..........|
00000060  14 b8 07 70 32 d1 84 48  a2 6e 2d 3d 42 a3 16 03  |...p2..H.n-=B...|
00000070  5d 08 e1 dd 45 26 a3 c6  f8 e7 f6 fb 7c 87 9f af  |]...E&......|...|
00000080  89 1d a9 67 b6 63 ae f1  2c 2f 0e 03 95 95 4e b5  |...g.c..,/....N.|
00000090  d4 3c 0d b1 e2 b1 8c 04  57 12 33 ae 0d a0 4c 31  |.<......W.3...L1|
```
After searching for file headers and a few bytes from the header I came accross [this](http://www.garykessler.net/library/file_sigs.html) page documenting a few file headers.
The header looks like zip file header except PK replaced with XX. If we replace XX with PK we find it's a valid zip file
```bash
$ sed -i -e 's/XX/PK/g' corrupted.data 
$ file corrupted.data 
corrupted.data: Zip archive data, at least v2.0 to extract
$ unzip corrupted.data
Archive:  corrupted.data
   creating: files/
  inflating: files/the_file          
$ cat files/the_file
the key is not RUZ{ecfb7b5b-719f-4523-a598-bd0957941099}
the key is certainly not RUZ{ecfb7b5b-0000-4523-a598-bd0957941099}
the key is NOT RUZ{ecfb7b5b-719f-4523-a598-bd0957941099}
the key is definitely not RUZ{ecfb7b5b-0000-4523-a598-bd0957941099}
the key is RUX{932a6dca-5e62-473d-98e0-09030b19dd03}
i promise that the KEY isn't RUZ{ecfb7b5b-0000-4523-a598-bd0957941099}
i promise that the key is not RUZ{ecfb7b5b-4523-0000-a598-bd0957941099}
```
Flag is **RUX{932a6dca-5e62-473d-98e0-09030b19dd03}**

### Bonus level
#### How iconic??
```bash
$ file favicon.ico
favicon.ico: MS Windows icon resource - 1 icon
$ strings favicon.ico
---8<---
ZZZZZZZZZZZZZZZ
RUX{SUPERHIDDENTOKEN}
---8<---
```
Flag is **RUX{SUPERHIDDENTOKEN}**
