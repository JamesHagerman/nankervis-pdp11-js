# PDP 11 Emulators in Javascript by Paul Nankervis

This repository contains the code originally written by Paul Nankervis to emulate a number of the PDP 11 series microcomputers by Digital Equipment Corporation (DEC).

The code in this repository originally lived (and continues to live until further notice) on Paul's site located at this address: [https://skn.noip.me/pdp11/pdp11.html](https://skn.noip.me/pdp11/pdp11.html)

Permission was given, by Paul to upload his source code to GitHub. This is the result of that work.

## Original notes by Paul Nankervis

Most of the following notes were extracted from Paul's original PDP 11 page. Some formatting issues may exist, and some re-organization may have taken place for clarity. Some links may be broken, but should be working on the original site.

In case things are unclear, be sure to check things on Paul's original site as things may have been lost in translation.

## PDP 11/70 Emulator v1.8   October 2017

This emulator came about because years ago I was a programmer for RSTS/E on a PDP 11/45 and had admired the console idle loop light pattern - but I couldn't quite remember how it looked. Given the unavailability of real systems it became time to write an emulator!

I was going to start with a PDP 11/45 emulator but the extra memory of a PDP 11/70 became far too attractive (a whole 4MB!). It took some time before I finally produced a [PDP 11/45](http://skn.noip.me/pdp11/pdp11-45.html) version.

I have met my core objective - I can now see the RSTS/E console light pattern that I was looking for, and found that newer versions (eg v9.6) have a different light pattern. Also I have now seen some of the light patterns for other OSes. RSX and BSD 2.11 have their own different patterns and Unix V5 and Ultrix operate with absolute minimum light movement (I'm assuming they operates mostly in WAIT mode).

Getting all of the operating systems used here presents its own set of challenges - one of which is finding the software in the first place. But one of the most interesting was RSTS/E V06C which has its own [story](https://skn.noip.me/pdp11/RSTSv06c.html).

Note: The boot code in this emulator is a custom PDP 11 program running with it's own set of light patterns. It is initially loaded at address 140000 and the LIGHTS command operates by mapping a WAIT instruction to different addresses within Supervisor mode. The source for this program can be found in the RT11 operating environment as BOOT.MAC You can use this code to boot one of the guest OSes or use the LIGHTS command and DIAG command to experiment with idle light patterns and load test the CPU.

If you wish to toggle in a simple light chaser to the front panel then here are some switch commands which can be used:

```
Address Data    Code        Switch commands
                            HALT, 001000, LOAD ADDRESS
001000  012700  mov #1,r0   012700, DEPOSIT
001002  000001              000001, DEPOSIT
001004  006100  rol r0      006100, DEPOSIT
001006  000005  reset       000005, DEPOSIT
001010  000775  br .-4      000775, DEPOSIT
                            001000, LOAD ADDRESS, ENABLE, START
```

To restart the initial boot code (if it has not been overwritten) use the switch commands:

```
    	HALT, 140000, LOAD ADDRESS, ENABLE, START
```

If you plan to run the emulator repeatedly or for a project, consider downloading the emulator to your own machine or server. This will significantly speed any of the emulator disk accesses and response times. All files and emulator OS disks can be found in the top level folder of http://skn.noip.me/pdp11/ or in the single zip file http://skn.noip.me/pdp11/pdp11.zip

This emulator matches approximately the following SIMH configuration:

```
  set cpu 11/70 1912K nofpp  !1912K is not actually SIMH legal - use 2M instead
  set clk 50hz
  attach rk0 rk0.dsk	!RK05 image of Unix V5
  attach rk1 rk1.dsk	!RK05 image of RT11 v4.0
  attach rk2 rk2.dsk	!RK05 image of RSTS V06C-03
  attach rk3 rk3.dsk	!RK05 image of XXDP
  attach rk4 rk4.dsk	!RK05 image of RT-11 3B
  attach rk5 rk5.dsk	!RK05 image of RT-11 V5.4F
  set rl0 RL02
  attach rl0 rl0.dsk	!RL02 image of BSD 2.9
  set rl1 RL02
  attach rl1 rl1.dsk	!RL02 image of RSX 11M v3.2
  set rl2 RL01
  attach rl2 rl2.dsk	!RL01 image of RSTS/E v7.0
  set rl3 RL02
  attach rl3 rl3.dsk	!RL02 image of XXDP+
  set rp0 RP06
  attach rp0 rp0.dsk	!RP06 image of ULTRIX-11 V3.1
  set rp1 RP06
  attach rp1 rp1.dsk	!RP06 image of BSD 2.11
  set rp2 RP04
  attach rp2 rp2.dsk	!RP04 image of RSTS/E v9.6
  set rp3 RP04
  attach rp3 rp3.dsk	!RP04 image of RSX 11M v4.6
  attach tm0 tm0.tap	!Backup of RSTS 4B-17
  attach tm1 tm1.tap	!Distribution for RSTS V06C-03
  attach tm2 tm2.tap	!Distribution for RSTS V7.0
```

This emulator then loads in the BOOT.MAC code to location 140000 and begins execution there.
There are many PDP emulators out there and I have never seen what I consider to be a complete list. Some of the really interesting ones can be found by googling terms such as "vhdl pdp 11". However the gold standard seems to be SIMH at [Trailing Edge](http://simh.trailing-edge.com/). A different approach to Javascript PDP 11 emulation can be found at [www.pcjs.org](http://www.pcjs.org/devices/pdp11/).

I believe that the first PDP 11 emulator would be SIM-11 written in FORTRAN before the first PDP 11/20 hardware was even built - see [How the PDP-11 Was Born](http://www.hampage.hu/pdp-11/birth.html). There is more PDP 11 history at [www.hampage.hu](http://www.hampage.hu/pdp-11/main.html).

Of course if you want your own PDP 11/70 front panel you might consider [one of these](https://hackaday.io/project/8069-pidp-11).

Happy emulating!

Paul Nankervis

## List of guest OS's:

*Note: Due to the size of some of these images, they are not included in this GitHub repository themselves. Instead, they can be found on the [Releases](https://github.com/JamesHagerman/nankervis-pdp11-js/releases) page of this repo, or on Paul's original site. If using the Releases version, extract the archive and place the `*.dsk` and `*.tap` files in the `os-images/` directory inside this repository.*

```
Disk	OS	Comment
RK0	Unix V5	Boot using: unix then login as root
RK1	RT11 v4.0	The lightest/fastest OS here
RK2	RSTS V06C-03	Boot and login as 1,2 with password SYSTEM or as 11,70 using PDP
RK3	XXDP	Diagnostic OS and utilities
RK4	RT-11 3B	Distribution for RT-11 Version 3B
RK5	RT-11 V5.4F	Distribution for RT-11 Version 5.4F
TM0	RSTS 4B-17	Boot ROLLIN from TM0 and restore DK0 with "DK:<MT:VIXEN/REW". Reboot from DK0 with "/BO:DK" and login as 1,2 with password SYSTEMor 11,70 using PDP Commands are case sensitive.
RL0	BSD 2.9	Boot using: rl(0,0)rlunix   CTRL/D to get to multiuser
RL1	RSX 11M v3.2	Login as 1,2 with password SYSTEM
RL2	RSTS/E v7.0	Option: <LF> Suboption: <LF> ... Login as 1,2 using SYSTEM or 11,70 using PDP
RL3	XXDP	Larger version of diagnostics - including PDP 11/70 utilities
RP0	ULTRIX-11 V3.1	CTRL/D to enter multiuser mode. Login as root with no password
RP1	BSD 2.11	Will autoboot and enter multiuser mode. Login as root with no password
RP2	RSTS/E v9.6	Answer boot questions and login as 1,2 (password SYSTEM) or 11,70 (no password)
RP3	RSX 11M v4.6	Starts logged in as 1,2 (password SYSTEM) - user accounts 200,1 (no password) or 11,70 (password PDP)
```

Note: Disks are shown in approximately order size. The [RK05](https://en.wikipedia.org/wiki/RK05) disks at the top are small and not too bad to use across a network. The [RP06](http://www.columbia.edu/cu/computinghistory/rp06.html) disks at the bottom can be rather slow.

[Youtube video 1](https://www.youtube.com/watch?v=3Nr1E96tXRU)

[Youtube video 2](https://www.youtube.com/watch?v=F-kJo1DTtw4)

## Bugs?

*Note: This text was originally hidden at the very bottom of Paul's Index. I didn't see it until moving it into this repo, so I moved it here for better visability.*

Plenty! Especially in the places where I haven't managed to figure out what a real PDP 11/70 should do. :-( Core PDP 11/70 stuff is well documented but some lesser used system functions require reverse engineering to understand.

If you have something you want me to look at let me know and I'll prioritise. However always happy to accept fixes!

*Note: Now that this code exists in GitHub, it may be prudent to both follow Paul's original advice and reach out to him, as well as open a GitHub issue. Keep in mind, the later may not be productive...*


## Example Boot logs from the various OS disk images

Example boot of Unix V5
BOOT> boot rk0
@unix

login: root
# date
Fri Mar 21 12:09:02 EST 1975
# chdir /etc
# pwd
../etc
# ls -al
total 40
drwxr-xr-x  2 bin       240 Mar 21 12:07 .
drwxr-xr-x  9 bin       160 Jan 29 16:14 ..
-rwxr--r--  1 bin       474 Nov 26 18:13 getty
-rwxr-xr-x  1 bin      1446 Nov 26 18:13 glob
-rwxr--r--  1 bin      1972 Nov 26 18:13 init
-rwxr-xr-x  1 bin       814 Nov 26 18:13 lpd
-rwxr--r--  1 bin      4136 Nov 26 18:13 mkfs
-rwxr--r--  1 bin      1800 Nov 26 18:13 mknod
-rwsr-xr-x  1 root     2078 Nov 26 18:13 mount
-rw-r--r--  1 bin        49 Nov 26 18:13 passwd
-rw-r--r--  1 bin        70 Nov 26 18:13 rc
-rw-r--r--  1 bin        56 Nov 26 18:13 ttys
-rwsr-xr-x  1 root     1990 Nov 26 18:13 umount
-rwxr-xr-x  1 bin        32 Nov 26 18:13 update
-rw-r--r--  1 root      144 Mar 21 12:09 utmp
# cat /etc/passwd
root::0:1::/:
daemon::1:1::/bin:
bin::3:1::/bin:
# cal 10 1981
      Oct 1981
 S  M Tu  W Th  F  S
             1  2  3
 4  5  6  7  8  9 10
11 12 13 14 15 16 17
18 19 20 21 22 23 24
25 26 27 28 29 30 31
# ls -al /bin
total 339
drwxr-xr-x  2 bin       944 Nov 26 18:13 .
drwxr-xr-x  9 bin       160 Jan 29 16:14 ..
-rwxr-xr-x  1 bin      1514 Nov 26 18:13 ar
-rwxr-xr-x  1 bin      7308 Nov 26 18:13 as
-rwxr-xr-x  1 bin      6042 Nov 26 18:13 bas
-rwxr-xr-x  1 bin       152 Nov 26 18:13 cat
-rwxr-xr-x  1 bin      5668 Nov 26 18:13 cc
...
Example boot of RT11 v4.0
BOOT> boot rk1
RT-11SJ  V04.00C

.D 56=5015

.TYPE V4USER.TXT
Welcome to RT-11 Version 4. RT-11 V04 provides new hardware support
and some major enhancements over Version 3B
...
.D 56=0

.MAC BOOT
ERRORS DETECTED:  0

.LINK BOOT

.DIR BOOT

BOOT  .MAC    16                 BOOT  .OBJ     4
BOOT  .SAV     4
 3 Files, 24 Blocks
 2851 Free blocks

.DIR

SWAP  .SYS    25  01-Feb-82      RT11BL.SYS    65  01-Feb-82
RT11SJ.SYS    67  01-Feb-82      RT11FB.SYS    80  01-Feb-82
TT    .SYS     2  01-Feb-82      DT    .SYS     3  01-Feb-82
DP    .SYS     3  01-Feb-82      DX    .SYS     3  01-Feb-82
...

.R ADVENT

WELCOME TO ADVENTURE!!  WOULD YOU LIKE INSTRUCTIONS?

YES
SOMEWHERE NEARBY IS COLOSSAL CAVE, WHERE OTHERS HAVE FOUND FORTUNES IN
TREASURE AND GOLD, THOUGH IT IS RUMORED THAT SOME WHO ENTER ARE NEVER 
SEEN AGAIN.  MAGIC IS SAID TO WORK IN THE CAVE.  I WILL BE YOUR EYES
AND HANDS.  DIRECT ME WITH COMMANDS OF 1 OR 2 WORDS.  I SHOULD WARN 
YOU THAT I LOOK AT ONLY THE FIRST FOUR LETTERS OF EACH WORD, SO YOU'LL
HAVE TO ENTER "NORTHEAST" AS "NE" TO DISTINGUISH IT FROM "NORTH". 
(SHOULD YOU GET STUCK, TYPE "HELP" FOR SOME GENERAL HINTS.  FOR INFOR-
MATION ON HOW TO END YOUR ADVENTURE, ETC., TYPE "INFO".)
      - - - 
THIS PROGRAM WAS ORIGINALLY DEVELOPED BY WILLIE CROWTHER.  MOST OF THE
FEATURES OF THE CURRENT PROGRAM WERE ADDED BY DON WOODS (DON @ SU-AI).
THE CURRENT VERSION WAS DONE BY MIKE WESTON.

YOU ARE STANDING AT THE END OF A ROAD BEFORE A SMALL BRICK BUILDING.
AROUND YOU IS A FOREST.  A SMALL STREAM FLOWS OUT OF THE BUILDING AND 
DOWN A GULLY. 

EAST
YOU ARE INSIDE A BUILDING, A WELL HOUSE FOR A LARGE SPRING. 

THERE ARE SOME KEYS ON THE GROUND HERE. 

THERE IS A SHINY BRASS LAMP NEARBY. 

THERE IS FOOD HERE. 

THERE IS A BOTTLE OF WATER HERE.

TAKE FOOD
OK
...
Example boot of RSTS V06C-03
BOOT> boot rk2

RSTS V06C-03 vixen (DK2)

Option: <LF>

You currently have: JOB MAX = 32, SWAP MAX = 28K.

You currently have crash dump disabled.

DD-MMM-YY? 31-OCT-76
12:00 PM? 9:03
INIT    V06C-03 RSTS V06C-03 vixen

Command File Name? <CR>
DETACHING...
...

I11/70
Password: PDP

Ready

DIR
 Name .Ext  Size    Prot   Date       SY:[11,70]
ACEY  .BAS     5   < 60> 31-Oct-76
TREK  .BAS    16   < 60> 31-Oct-76
TREK  .DOC     9   < 60> 31-Oct-76
ANIMAL.BAS     5   < 60> 31-Oct-76
STRTRK.BAS    27   < 60> 31-Mar-81
STRTR1.BAS     9   < 60> 31-Mar-81
ADVENT.DOC     4   < 60> 20-Jul-85
ADVENT.SAV    93   <124> 20-Jul-85
ADVENT.VAR    22   < 60> 20-Jul-85
ADVTXT.TXT   125   < 60> 20-Jul-85
SYSMAC.SML    42   < 60> 13-Mar-77
HELLO .MAC     1   < 60> 13-Mar-77
BOOT  .MAC    24   < 60> 13-Mar-77

Total of 35 blocks in 4 files in SY:[11,70]

Ready

SYSTAT

RSTS V06C-03 vixen status at 31-Oct-76, 09:03 AM Up: 18

Job    Who    Where    What    Size    State    Run-Time   RTS
 1    [OPR]   Det     ERRCPY    5K     SR            3.4  BASIC 
 2    [SELF]  KB0     SYSTAT    8K     RN Lck        0.3  BASIC 

Busy Devices: None

Disk Structure:
Disk    Open    Free    Cluster Errors  Name    Comments
DK2       3     239        1      0     VIXEN   Pub, DLW

Small   Large   Jobs    Hung TTY's      Errors
345       0      2/2        0              0

Run-Time Systems:
 Name   Ext       Size  Users   Comments
BASIC   BAC     16(16)K   2     Perm, Addr:25, KBM, CSZ
RSX     TSK      2(28)K   0     Non-Res, KBM
RT11    SAV      4(28)K   0     Non-Res, KBM, CSZ, EMT:255
RMS11   TSK      4(28)K   0     Non-Res

Message Receivers:
 Name   Job     Msgs    Max     Senders
ERRLOG   1       0      40      Priv

Ready

RUN ACEY
                          ACEY DUCEY CARD GAME
               CREATIVE COMPUTING  MORRISTOWN, NEW JERSEY


ACEY-DUCEY IS PLAYED IN THE FOLLOWING MANNER 
THE DEALER (COMPUTER) DEALS TWO CARDS FACE UP
YOU HAVE AN OPTION TO BET OR NOT BET DEPENDING
ON WHETHER OR NOT YOU FEEL THE CARD WILL HAVE
A VALUE BETWEEN THE FIRST TWO.
IF YOU DO NOT WANT TO BET, INPUT A 0
YOU NOW HAVE 100 DOLLARS.
....
HERE ARE YOUR NEXT TWO CARDS: 
 8 
KING

WHAT IS YOUR BET? 95
JACK
YOU WIN!!!
YOU NOW HAVE 190 DOLLARS.

HERE ARE YOUR NEXT TWO CARDS: 
 2 
 10 

WHAT IS YOUR BET? ^C

Ready

BYE
Confirm: Y
Saved all disk files; 35 blocks in use, 65 free
Job 2 User 11,70 logged off KB0 at 31-Oct-76 09:04 AM
System RSTS V06C-03 vixen
Run time was 1.5 seconds
Elapsed time was 1 minute
Good morning

Example boot of XXDP
BOOT> boot rk3

CHMDKB1 XXDP+ DK MONITOR
BOOTED VIA UNIT 3
28K UNIBUS SYSTEM

ENTER DATE (DD-MMM-YY): <CR>

RESTART ADDR: 152010
THIS IS XXDP+.  TYPE "H" OR "H/L" FOR HELP.

.D

ENTRY# FILNAM.EXT        DATE          LENGTH  START

    1  HDDKB0.SYS       2-JAN-70          2    000112
    2  HMDKB1.SYS       2-JAN-70         17    000113
    3  HDDKB1.SYS       2-JAN-70          2    000114
    4  HSAAC4.SYS       8-DEC-82         24    000115
....
Example boot of RSTS 4B-17
BOOT> boot tm0

ROLLIN V07

#DK:<MT:VIXEN/REW
END-OF-FILE DURING READ, TYPE
M TO MOUNT ANOTHER REEL, OR K TO KILL REQUEST: 

#/BO:DK

RSTS V04B-17 VIXEN

OPTION? ST
DD-MON-YY? 31-OCT-71
HH:MM? 6:42
VIXEN  - SYSTEM PACK MOUNTED
ENABLE CRASH DUMP? N
CHAIN "INIT"
CATASTROPHIC ERROR
PROGRAM LOST-SORRY
I/O CHANNEL NOT OPEN

Ready

SYSTEM INITIALIZATION PROGRAM

END OF FILE ON DEVICE - INIT ASSUMED COMPLETE

Ready

CAT
LOGIN .BAS       7       60     31-Oct-71 31-Oct-71 06:42 AM
LOGIN .BAC       15      60     31-Oct-71 31-Oct-71 06:42 AM
LOGOUT.BAS       7       60     31-Oct-71 31-Oct-71 06:42 AM
....

Ready

BYE
CONFIRM: Y
SAVED ALL DISK FILES; 782 BLOCKS IN USE
JOB 1 USER 1,2 LOGGED OFF KB0 AT 31-Oct-71 06:42 AM
SYSTEM RSTS V04B-17 VIXEN
RUN TIME WAS 1.4 SECONDS
ELAPSED TIME WAS 13 SECONDS
GOOD MORNING

HELLO

RSTS V04B-17 VIXEN  JOB 1  KB0  31-Oct-71  06:42 AM
#11,70
PASSWORD:
RSTS V4B-17 IS NOW AVAILABLE...

NEW OR OLD--
CAT
PRIME .BAS       1       60     31-Oct-71 31-Oct-71 06:43 AM
PI    .BAS       1       60     31-Oct-71 31-Oct-71 06:43 AM

Ready

RUN PI
3.14159265358979

Ready

RUN $SYSTAT
OUTPUT STATUS TO? 

RSTS V04B-17 VIXEN STATUS ON 31-Oct-71 AT 06:42 AM UP: 42

JOB    WHO      WHERE    WHAT   SIZE    STATE     RUN-TIME
 1    11,70      KB0    SYSTAT   6K     RN             3.4

BUSY DEVICES: NONE

DISK STRUCTURE:
DISK    OPEN     FREE   CLUSTER ERRORS  COMMENTS
DK0      0       3184     1       0     PUBLIC

SMALL  LARGE   ERRORS  HUNG TTY'S
 69     0      0        0 

Ready
Example boot of BSD 2.9
BOOT> boot rl0
:boot

70Boot
: rl(0,0)rlunix

CONFIGURE SYSTEM:
xp 0 csr 176700 vector 254 attached
rk 0 csr 177400 vector 220 attached
hk ? csr 177440 vector 210 skipped:  No CSR
rl 0 csr 174400 vector 160 attached
rp ? csr 176700 vector 254 interrupt vector already in use
ht 0 csr 172440 vector 224 skipped:  No CSR
tm 0 csr 172520 vector 224 skipped:  No CSR
...
Erase=^?, kill=^U, intr=^C
# ls -al
total 546
drwxr-xr-x11 root     daemon       512 Mar  7 09:00 .
drwxr-xr-x11 root     daemon       512 Mar  7 09:00 ..
-rw-rw-r-- 1 root     daemon       164 Sep 29 09:20 .cshrc
-rw-rw-r-- 1 root     daemon       266 Mar  7 08:43 .login
-rw-rw-r-- 1 root     superuse       2 Jul 26 16:00 .msgsrc
-rw-rw-r-- 1 root     daemon       116 Mar 30 00:59 .profile
-rw-r--r-- 1 root     superuse      56 Nov 20 16:03 2.9stamp
-rw-rw-r-- 1 root     superuse     450 Mar 30 00:50 READ_ME
drwxrwxr-x 2 bin      bin         1632 Nov 20 16:04 bin
-rwxrwxr-x 1 root     superuse   23572 Mar  7 09:05 boot
...
# cat /etc/passwd
root::0:2:The Man:/:/bin/csh
toor::0:2:The Man:/:
daemon:***:1:1:The devil himself:/:
sys:***:2:1::/:
bin:***:3:1::/:
uucp::4:1:UNIX-to-UNIX Copy:/usr/spool/uucppublic:/usr/lib/uucp/uucico
notes:***:5:1:Notesfiles maintainer:/usr/spool/notes:
anon:***:6:1:Notesfiles anonymous user:/usr/spool/notes:
news:***:7:1:News maintainer:/usr/spool/news:
wnj:ZDjXDBwXle2gc:8:2:Bill Joy,457E,7780:/a/guest/wnj:/bin/csh
dmr:AiInt5qKdjmHs:9:2:Dennis Ritchie:/a/guest/dmr:
ken:sq5UDrPlKj1nA:10:2:& Thompson:/a/guest/ken:
mike:KnKNwMkyCt8ZI:11:2:mike karels:/a/guest/mike:/bin/csh
carl:S2KiTfS3pH3kg:12:2:& Smith,508-21E,6258:/a/guest/carl:/bin/csh
joshua::999:2:&:/usr/games:/usr/games/wargames
# CTRL/D
Wed Dec 31 16:04:16 PST 1969
/etc/fstab: No such file or directory
/usr/sys: No such file or directory
init: /dev/tty07: cannot open
...

Berkeley Unix 2.9BSD

:login: root

Welcome to the 2.9BSD (Berkeley) UNIX system.

tty: Command not found.
# ls -al /bin
total 1182
-rwxrwxr-x 1 bin      bin         8692 Dec 31 16:59 #
drwxrwxr-x 2 bin      bin         1632 Nov 20 16:04 .
drwxr-xr-x11 root     daemon       512 Mar  7 09:00 ..
-rwxrwxr-x 2 bin      bin         2917 Dec 31 16:59 [
-rwxrwxr-x 1 bin      bin        30340 Mar 24 08:27 adb
-rwxrwxr-x 1 bin      bin         9844 Dec 31 16:58 ar
-rwxrwxr-t 1 bin      bin         5626 Sep 30 17:39 as
-rwxrwxr-x 1 bin      bin         4508 Jan 18 08:22 cat
-rwxrwxr-t 1 bin      bin         7314 Oct  9 04:04 cc
-rwxrwxr-x 1 bin      bin         5096 Dec 31 16:59 chgrp
-rwxrwxr-x 1 bin      bin         3364 Dec 31 16:59 chmod
...
Example boot of RSX 11M v3.2
BOOT> boot rl1

  RSX-11M V3.2 BL26   1912K  MAPPED
>RED DL1:=SY:
>RED DL1:=LB:
>MOU DL1:RSXM26
>@DL1:[1,2]STARTUP
>* Enter date and time ( dd-mmm-yy hh:mm ) [S]: 29-JAN-90 12:01
>TIM 29-JAN-90 12:01
>INS $PIP
>INS $EDT
>BYE
>
HAVE A GOOD AFTERNOON
29-JAN-90 12:01 TT0:  LOGGED OFF
>@ <EOF>
>HELLO 1,2
PASSWORD:

        RSX-11M BL26   MULTI-USER SYSTEM

GOOD AFTERNOON
29-JAN-90 12:01 LOGGED ON TERMINAL TT0:

Welcome to RSX-11M V3.2 timesharing
 
>PIP/LI

DIRECTORY DL1:[1,2]
29-JAN-90 12:01

HELLO.MAC;1         1.         30-OCT-76 12:02
SYE.HLP;1           8.         26-MAY-79 13:52
EDTCOM.MSG;1        16.        26-MAY-79 13:52
QIOSYM.MSG;1        29.        26-MAY-79 13:52
LOGIN.TXT;1         1.         31-OCT-81 12:11
HELP.HLP;1          1.         31-OCT-81 12:11
STARTUP.CMD;1       1.         31-OCT-81 12:04
FORTH.MAC;1         149.       30-OCT-76 12:02

TOTAL OF 206./223. BLOCKS IN 8. FILES

>INS $MAC
>MAC HELLO,HELLO=HELLO
>INS $TKB
>TKB HELLO=HELLO
>INS HELLO
>RUN HELLO
>
 HELLO WORLD!

>UNS HELLO
>BYE
>
HAVE A GOOD AFTERNOON
29-JAN-90 12:02 TT0:  LOGGED OFF
>
Example boot of RSTS/E v7.0
BOOT> boot rl2

RSTS V7.0-07 Vixen (DL2)

Option: 

You currently have: JOB MAX = 63, SWAP MAX = 31K.

Default memory allocation table specifies some existing memory
as being nonexistent.

Table will be reset by RSTS.

  Memory allocation table:

     0K: 00000000 - 00207777 (  34K) : EXEC
    34K: 00210000 - 00307777 (  16K) : RTS (BASIC)
    50K: 00310000 - 16737777 (1862K) : USER
  1912K: 16740000 - End              : NXM


  Table suboption? 

You currently have crash dump disabled.

DD-MMM-YY? 31-OCT-81
12:00 PM? 
INIT    V7.0-07 RSTS V7.0-07 Vixen

Command File Name? 
DETACHING...

I11/70
Password: PDP

Ready

DIR
 Name .Ext    Size   Prot    Date       SY:[11,70]
ACEY  .BAS       5   < 60> 31-Oct-76
TREK  .BAS      16   < 60> 31-Oct-76
TREK  .DOC       9   < 60> 31-Oct-76
ANIMAL.BAS       5   < 60> 31-Oct-76
STRTRK.BAS      27   < 60> 31-Mar-81
STRTR1.BAS       9   < 60> 31-Mar-81
ADVENT.DOC       4   < 60> 20-Jul-85
ADVENT.SAV      93   <124> 20-Jul-85
ADVENT.VAR      22   < 60> 20-Jul-85
ADVTXT.TXT     125   < 60> 20-Jul-85
SYSMAC.SML      42   < 60> 13-Mar-77
HELLO .MAC       1   < 60> 13-Mar-77
BOOT  .MAC      24   < 60> 13-Mar-77

Total of 382 blocks in 13 files in SY:[11,70]

Ready

SYSTAT

RSTS V7.0-07 Vixen status at 31-Oct-81, 12:00 PM Up: 31

Job    Who    Where    What    Size    State    Run-Time    RTS
 1    [OPR]   Det     ERRCPY    5K     SR            4.8   BASIC 
 2    [SELF]  KB0     SYSTAT   11K     RN Lck        0.9   BASIC 

Busy Devices: None

Disk Structure:
Disk    Open    Free    Cluster Errors  Name    Comments
DL2       3     2502       1      0     VIXEN   Pub, DLW

Small   Large   Jobs    Hung TTY's      Errors
488       1      2/2        0              0

Run-Time Systems:
 Name   Ext       Size  Users   Comments
BASIC   BAC     16(16)K   2     Perm, Addr:34, KBM, CSZ
RT11    SAV      4(28)K   0     Non-Res, KBM, CSZ, EMT:255
RSX     TSK      3(28)K   0     Non-Res, KBM
TECO    TEC      8(24)K   0     Non-Res

Resident Libraries: None

Message Receivers:
 Name   Job     Msgs    Max     Senders
ERRLOG   1       0      40      Priv

Ready

EDT TEST.BAS
*I
10 PRINT "HELP"
20 END
^Z
*EXIT
2 LINES OUTPUT

Ready

OLD TEST

Ready

RUN
TEST    12:01 AM        31-Oct-81
HELP

Ready

BYE
Confirm: Y
Saved all disk files; 385 blocks in use, 115 free
Job 2 User 11,70 logged off KB0 at 31-Oct-81 12:01 AM
System RSTS V7.0-07 Vixen
Run time was 1.7 seconds
Elapsed time was 1 minute
Good morning


Example boot of XXDP
BOOT> boot rl3

CHMDLD0 XXDP+ DL MONITOR
BOOTED VIA UNIT 3
28K UNIBUS SYSTEM

ENTER DATE (DD-MMM-YY): <CR>

RESTART ADDR: 152010
THIS IS XXDP+.  TYPE "H" OR "H/L" FOR HELP.

.D

ENTRY# FILNAM.EXT        DATE          LENGTH  START

    1  MMDP  .SAV       3-MAR-83C        17    000310
    2  MTDP  .SAV       3-MAR-83C        17    000331
    3  HSAAD0.SYS       3-MAR-83         24    000352
    4  HSABC0.SYS       3-MAR-83         28    000402
    5  HSACC0.SYS       3-MAR-83         27    000436
    6  HSADB0.SYS       3-MAR-83         25    000471
    7  HUDIB0.SYS       3-MAR-83          5    000522
    8  HELP  .TXT       3-MAR-83         14    000527
...
.R EKBA??
EKBAD0.BIC
AA
CEKBAD0 11/70 CPU #1

END PASS
END PASS
END PASS
restart through switches

.R EKBE??
EKBEE1.BIC

CEKBEE0 11/70 MEM MGMT

CPU UNDER TEST FOUND TO BE A KB11-CM

END PASS #     1  TOTAL ERRORS SINCE LAST REPORT      0
END PASS #     2  TOTAL ERRORS SINCE LAST REPORT      0
END PASS #     3  TOTAL ERRORS SINCE LAST REPORT      0
END PASS #     4  TOTAL ERRORS SINCE LAST REPORT      0
END PASS #     5  TOTAL ERRORS SINCE LAST REPORT      0
....
restart through switches

.R EQKC??
EQKCE1.BIC

CEQKC-E...PDP 11/70 CPU EXERCISER

CPU UNDER TEST FOUND TO BE A 11/74          (KB11CM)

PROCESSOR ID REGISTER =000001 (OCTAL)       1 (DECIMAL) 
OPT.CP=145406
OPERATIONAL SWITCH SETTINGS
SWITCH                  USE
  15            HALT ON ERROR
  14            LOOP ON TEST
  13            INHIBIT ERROR TYPEOUTS
  12            INHIBIT UBE
  11            INHIBIT ITTERATIONS
  10            BELL ON ERROR
   9            LOOP ON ERROR
   8            ALLOW RELOCATION VIA I/O DEVICE (NOTE CHANGE)
   7            INHIBIT TYPEOUT OF THIS TEXT AND SYS SIZE
   6            INHIBIT RELOCATION
   5            INHIBIT ROUND ROBIN RELOCATION
   4            INHIBIT RANDOM DISK ADDRESS
   3            INHIBIT MBT
   2            THESE THREE SWITCHES
   1            ARE ENCODED TO SELECT RELOCATION
   0            ON THE FOLLOWING DEVICES:
        0...RP11/RP03
        1...RK11/RK05
        2...NOT USED
        3...NOT USED
        4...RH70/RP04
        5...RH70/RS04 OR RS03
        6...NOT USED
        7...NOT USED

**NOTE** SWITCH REG BIT 8 HAS BEEN REVERSED IN REV D
NOTE THAT SWR BIT 8 SET NOW ALLOWS I/O RELOCATION

THIS PROGRAM SUPPORTS I/O RELOCATION ONLY WITH THE FOLLOWING DEVICES:
RP03,RK05,RP04/5/6,RS03/4
THE FOLLOWING DEVICES AND DRIVES WILL BE USED FOR RELOCATION IF BIT 8 SET:
DEVICE  DRIVES
RK05    0, 1, 2, 3, 4, 5, 6, 7, 
RP04    0, 1, 2, 3, 
TYPE A CHARACTER TO CONTINUE

1THE QUICK BROWN FOX JUMPED OVER THE LAZY DOGS BACK 0123456789
2THE QUICK BROWN FOX JUMPED OVER THE LAZY DOGS BACK 0123456789
3THE QUICK BROWN FOX JUMPED OVER THE LAZY DOGS BACK 0123456789
4THE QUICK BROWN FOX JUMPED OVER THE LAZY DOGS BACK 0123456789
5THE QUICK BROWN FOX JUMPED OVER THE LAZY DOGS BACK 0123456789
000:01:33

END PASS #     1  TOTAL ERRORS SINCE LAST REPORT      0
1THE QUICK BROWN FOX JUMPED OVER THE LAZY DOGS BACK 0123456789
2THE QUICK BROWN FOX JUMPED OVER THE LAZY DOGS BACK 0123456789
...

Example boot of ULTRIX-11 System V3.1
BOOT> boot rp0

Sizing Memory...

Boot: hp(0,0)unix    (CTRL/C will abort auto-boot)

Load device (? for help, <RETURN> if none) < ht tm ts tk rx rl rc > ? <CR>

hp(0,0)unix: 14784+17024+8192+8000+8064+8000+8064+8128+8000+7808+7936+7936+7680+7360+1344

ULTRIX-11 Kernel V3.1

realmem = 3915776
buffers = 25600
clists  = 1600
usermem = 3756608
maxumem = 212992
erase = delete, kill = ^U, intr = ^C
# CTRL/D

Restricted rights:

        Use, duplication, or disclosure is subject
        to restrictions stated in your contract with
        Digital Equipment Corporation.

*UNIX is a trademark of AT&T Bell Laboratories.

Mounted /dev/hp01 on /usr
Mounted /dev/hp04 on /user1

Sat Oct 31 12:06:33 GMT-0:00 1981

ERROR LOG has - 1 of 200 blocks used

ULTRIX-11 System V3.1 (vixen)

login: root

Welcome to the ULTRIX-11 System

erase = delete, kill = ^U, intr = ^C
vixen# uname -a
ULTRIX-11 vixen 3 0 PDP-11/70
vixen# ps -xl

   F S UID   PID  PPID CPU PRI NICE   ADDR  SZ  WCHAN TTY TIME CMD
   3 S   0     0     0 205   0   20   3756   4  73326 ?   0:10 swapper
   1 S   0     1     0   0  30   20   4770  13 114226 ?   0:00 /etc/init
1101 S   0     2     1   0   5    0   6162  22 112272 ?   0:00 /etc/elc
   1 S   0    41     1   0  30   20   6433  16 114352 co  0:00 -sh
   1 R   0    49    41   6  50   20  10370  28        co  0:00 ps -xl
 201 S   0    33     1   0  40   20   5251   7 140000 ?   0:00 /etc/update
 201 S   0    37     1   0  40   20   7017  13 140000 ?   0:00 /etc/cron
vixen# w
 12:06pm  up  1 user,  load average: 0.00, 0.00, 0.00
User     tty       login@  idle   JCPU   PCPU  what
root     console  12:06pm            1         w
vixen# mount
hp01 on /usr
hp04 on /user1
vixen# df
Filesystem    total    kbytes  kbytes  percent
   node       kbytes    used    free   used    Mounted on
/dev/hp00       4606    3077    1529    67%    /
/dev/hp01       9629    3594    6035    37%    /usr
/dev/hp04     148244       2  148242     0%    /user1
vixen# set
HOME=/
IFS=

PATH=:/usr/ucb:/bin:/usr/bin:/etc
PS1=vixen#
PS2=>
SHELL=/bin/sh
TERM=dw3
TZ=GMT0
USER=root
vixen# cat /.profile
PS1=`hostname`'# '
echo "erase = delete, kill = ^U, intr = ^C"
if test `tty` = /dev/console
then
        stty dec prterase
else
        stty crt tabs
fi
PATH=:/usr/ucb:/bin:/usr/bin:/etc
export PATH
vixen# ls /etc
accton       fpsim        init         msf          protocols    termcap
arp          fsdb         ipatch       mtab         rawfs        tss
catman       fstab        labelit      networks     rc           ttys
cron         getty        log          newfs        rdate        ttytype
cshprofile   gettytab     lpdrestart   nu           remote       tzname
dcopy        group        lpset        nu.cf        route        umount
ddate        hosts        mkfs         nulib        rx2fmt       update
dmesg        hosts.equiv  mknod        passwd       services     utmp
elc          ifconfig     motd         printcap     syslog.conf  vipw
eli          inetd.conf   mount        profile      syslog.pid   volcopy
vixen#
Example boot of BSD 2.11
BOOT> boot rp1

70Boot from xp(0,1,0) at 0176700
Press <CR> to boot, or any other key to abort: 0
: xp(0,1,0)unix
Boot: bootdev=05010 bootcsr=0176700

2.11 BSD UNIX #2: Oct 31 04:05:24 PST 1981
    root@Sat:/usr/src/sys/VIXEN

phys mem  = 3915776
avail mem = 3684480
user mem  = 307200

hk ? csr 177440 vector 210 skipped:  No CSR.
ht ? csr 172440 vector 224 skipped:  No CSR.
ra ? csr 172150 vector 154 skipped:  No CSR.
rl 0 csr 174400 vector 160 attached
tm ? csr 172520 vector 224 skipped:  No CSR.
tms ? csr 174500 vector 260 skipped:  No CSR.
ts ? csr 172520 vector 224 skipped:  No CSR.
xp 0 csr 176700 vector 254 attached
Automatic reboot in progress...
Sat Oct 31 04:28:59 PST 1981
Sat Oct 31 04:28:59 PST 1981
checking quotas: done.
Assuming non-networking system ...
checking for core dump...
preserving editor files
clearing /tmp
standard daemons: update cron accounting.
starting lpd
starting local daemons: sendmail.
Sat Oct 31 04:29:02 PST 1981

2.11 BSD UNIX (vixen.2bsd.com) (console)

login: root
erase, kill ^U, intr ^C
# uname
2.11BSD
# ps -al
  F S   UID   PID  PPID CPU PRI NICE  ADDR  SZ WCHAN    TTY TIME COMMAND
  1 R     0    80    75   3  50   0  21600  59          co  0:00 ps -al
# cat /etc/passwd
root:*:0:1:The Man:/:/bin/sh
daemon:*:1:1:The devil himself:/:/bin/sh
sys:*:4:2:Operating System:/tmp:nologin
operator:*:2:5:System &:/usr/guest/operator:/bin/csh
bin:*:3:20:Binaries Commands and Source:/:/bin/csh
nobody:*:32767:31:Nobody:/nonexistent:/bin/sh
uucp:*:66:1:UNIX-to-UNIX Copy:/usr/spool/uucppublic:/usr/sbin/uucico
ingres:*:39:74:INGRES Owner:/usr/ingres:/bin/csh
# ls -al /sys/conf
total 147
drwxr-xr-x  5 root          512 Mar 31 13:55 .
drwxr-xr-x 23 root          512 Mar 31 15:45 ..
-r--r--r--  1 root          238 Dec 27  1986 :comm-to-bss
...
# cat > hello.c
#include <stdio.h>
main()
{
    printf("Hello world\n");
}
CTRL/D
# cc hello.c
# ls -al hello* a.out
-rwxr-x--x  1 root         5335 Mar 31 15:52 a.out
-rw-r-----  1 root           59 Mar 31 15:52 hello.c
# ./a.out
Hello world
# cd /sys/VIXEN
# make
make -f Make.sys I=/usr/include H=../h M=../machine AS="/bin/as -V" .....
cc -O -DKERNEL -DVIXEN -DFPSIM -DSOFUB_MAP -I. -I../h -S ../sys/init_main.c
/bin/sed -f SPLFIX init_main.s | /bin/as -V -u -o init_main.o
rm -f init_main.s
cc -O -DKERNEL -DVIXEN -DFPSIM -DSOFUB_MAP -I. -I../h -S ../sys/init_sysent.c
/bin/sed -f SPLFIX init_sysent.s | /bin/as -V -u -o init_sysent.o
rm -f init_sysent.s
cc -O -DKERNEL -DVIXEN -DFPSIM -DSOFUB_MAP -I. -I../h -S ../sys/kern_acct.c
/bin/sed -f SPLFIX kern_acct.s | /bin/as -V -u -o kern_acct.o
rm -f kern_acct.s
cc -O -DKERNEL -DVIXEN -DFPSIM -DSOFUB_MAP -I. -I../h -S ../sys/kern_clock.c
/bin/sed -f SPLFIX kern_clock.s | /bin/as -V -u -o kern_clock.o
rm -f kern_clock.s
...
size unix
text    data    bss     dec     hex
50624   7792    23708   82124   140cc   total text: 118272
        overlays: 7680,7360,7680,7488,7488,7744,5632,6144,7680,2752
Compacting symbol table
symcompact unix
symcompact: 209 symbols removed
Compacting strings table
strcompact unix
rearranging symbols
symorder ../pdp/symbols.sort unix
./checksys unix
System will occupy 210528 bytes of memory (including buffers and clists).

               end {0075414}          nbuf {0017116}           buf {0044360}
             nproc {0017104}          proc {0061314}         ntext {0017106}
              text {0074354}         nfile {0017112}          file {0071720}
            ninode {0017110}         inode {0017200}      ncallout {0017114}
           callout {0035764}     ucb_clist {0017122}        nclist {0017120}
          ram_size {0000000}       xitdesc {0017176}      quotdesc {0000000}
         namecache {0036504}       _iosize {0010004}          nlog {0016156}
# make install
install -c -o root -g kmem -m 744 unix /unix
# ps -aux
USER       PID NICE SZ TTY TIME COMMAND
root         0   0   8 ?   0:00 swapper
root         1   0  29 ?   0:00  (init)
root        42   0  11 ?   0:00 update 
root        45   0  51 ?   0:00 cron 
root        49  -1  26 ?   0:00 acctd 
root        55   0  47 ?   0:00 /usr/sbin/lpd 
root        75   0  19 co  0:00 -sh 
root       884   0  59 co  0:00 ps -aux 
# 
Example boot of RSTS V9.6
BOOT> boot rp2

RSTS V9.6-11 RSTS   (DB2) INIT V9.6-11

Today's date? 31-OCT-86

Current time? 11:12

Start timesharing? <Yes> 

Default memory allocation table shows LESS
memory than INIT detects on this machine.

Adjusting memory table.

  Memory allocation table:

     0K: 00000000 - 00433777 (  71K) : EXEC
    71K: 00434000 - 15163777 (1622K) : USER
  1693K: 15164000 - 16737777 ( 219K) : XBUF

Memory available to RSTS/E is 1912K words.

86.10.31 11:12

1 device disabled

Proceed with system startup? <YES> 

 Beginning RSTS/E system startup...
86.10.31  11:12      Installing monitor overlays
86.10.31  11:12      Mounting disks
86.10.31  11:12      Assigning logical names
86.10.31  11:12      Starting error logging
86.10.31  11:12      Setting system characteristics
31-Oct-86 11:12 AM   Installing run-time systems and libraries
31-Oct-86 11:12 AM   Setting terminal characteristics
31-Oct-86 11:12 AM   Defining system commands
31-Oct-86 11:12 AM   Setting printer characteristics
31-Oct-86 11:12 AM   Starting spoolers

*** From [1,2] on KB0: at 11:12 AM 31-Oct-86     
** RSTS/E is on the air...     



I11/70

$ systat

RSTS V9.6-11 RSTS/E V9.6 status at 31-Oct-86, 11:14 AM Up: 2:45

Job    Who    Where     What    Size    State    Run-Time    RTS
 1     1,2    Det      ERRCPY    5K     SR            0.8    ...RSX
 2    11,70   KB0      SYSTAT   16K     RN Lck        0.4    ...RSX
 3     1,2    Det      PBS...   19K     SL            0.0    ...RSX

Busy Devices: None

Disk Structure:
Dsk  Open    Size      Free    Clu   Err Name      Level  Comments
DB2    18  171796  119452  69%   4     0 VIXEN      1.2   Pub, DLW

General  FIP                    Hung
Buffers  Buffers  Jobs/Jobmax   TTY's   Errors
  741      461       3/63         0        0

Run-Time Systems:
 Name   Typ   Dev    Size    Users  Comments
...RSX  TSK          0(32)K    3    Monitor, KBM
DCL     COM   DB2:   24(8)K    0    Temp, Addr:71, DF KBM
RT11    SAV   DB2:   4(28)K    0    Temp, Addr:108, KBM, CSZ, EMT:255
BASIC   BAC   DB2:  16(16)K    0    Temp, Addr:166, KBM, CSZ
TECO    TEC   DB2:  10(20)K    0    Non-Res, KBM

Resident Libraries:
 Name  Prot        Acct      Size  Users  Comments
CSPLIB < 42>  DB2:[  0,1  ]    8K    2    Temp, Addr:100
EDT    < 42>  DB2:[  0,11 ]   39K    0    Non-Res
RMSRES < 42>  DB2:[  0,10 ]    4K    1    Temp, Addr:1689
RMSLBA < 42>  DB2:[  0,10 ]    4K    1    Temp, Addr:139
RMSLBB < 42>  DB2:[  0,10 ]    3K    1    Temp, Addr:132
RMSLBC < 42>  DB2:[  0,10 ]    3K    1    Non-Res
RMSLBD < 42>  DB2:[  0,10 ]    2K    1    Temp, Addr:143
RMSLBE < 42>  DB2:[  0,10 ]    3K    1    Temp, Addr:125
RMSLBF < 42>  DB2:[  0,10 ]    4K    1    Temp, Addr:128
DAPRES < 42>  DB2:[  0,10 ]   10K    0    Non-Res, Addr:1679

Message Receivers: 
Rcvrid   Job    Rib  Obj   Msgs/Max   Links/InMax/OutMax  Access
ERRLOG    1      0    1       0/40          0/0/0          Prv
QM$CMD    3      1    3       0/20          0/0/255        Prv
QM$SRV    3      2    4       0/30          0/0/255        Prv
QM$URP    3      3    5       0/10          0/0/255        Lcl
PR$03A    3     17   65       0/5           0/0/255        Prv
PR$03B    3     25   65       0/5           0/0/255        Prv
BA$03A    3     41   66       0/5           0/0/255        Prv
BA$03B    3     49   66       0/5           0/0/255        Prv
BA$03C    3     57   66       0/5           0/0/255        Prv
$ help

You can obtain on-line information about any DCL command or qualifier, as
well as many other general topics.  For more complete details about a
topic, refer to the appropriate RSTS manual or guide.

The RSTS/E System User's Guide contains descriptions of the DCL commands
and qualifiers that you use in file, system, and programming operations.

The RSTS/E System Manager's Guide contains descriptions of the DCL
commands and qualifiers used in system management operations.

See the RSTS/E Quick Reference Guide for the syntax of all DCL commands
and qualifiers on RSTS/E.

For instructions on how to use this HELP facility, type HELP HELP from
DCL, or type HELP in response to the HELP Topic?  prompt.  
 

Additional help is available on:

@               Accounts        Advanced        ALLOCATE        APPEND
ASSIGN          ATTACH          BACKUP          BASIC           BROADCAST
BYE             CCL             CLOSE           COBOL           COPY
CREATE          Dates           DCL             DEALLOCATE      DEASSIGN
DEFINE          DELETE          DETACH          DIBOL           DIFFERENCES
DIRECTORY       DISMOUNT        DUMP            EDIT            Entries
EOD             EXIT            Expressions     Files           FORCE
Forms           FORTRAN         Functions       GOSUB           GOTO
HANGUP          HELP            IF              INITIALIZE      INQUIRE
INSTALL         Keys            Labels          LINK            LOAD
LOGIN           LOGOUT          MACRO           MAIL            MERGE
MOUNT           ON              OPEN            Operators       Passwords
PRINT           Privileges      Programs        Queues          Quotas
READ            REMOVE          RENAME          REQUEST         RESTORE
RETURN          RT11            RSX             RUN             Runtime Systems
Servers         SET             SHOW            SORT            START
STOP            SUBMIT          Symbols         Times           TYPE
UNLOAD          WRITE

Topic? ^Z

$ set term/width:80
$ dir

 Name .Typ    Size    Prot     Name .Typ    Size    Prot    SY:[11,70]
ACEY  .BAS       5   < 60>    TREK  .BAS      16   < 60>
TREK  .DOC       9   < 60>    ANIMAL.BAS       5   < 60>
STRTRK.BAS      27   < 60>    STRTR1.BAS       9   < 60>
ADVENT.DOC       4   < 60>    ADVENT.SAV      93   <124>
ADVENT.VAR      22   < 60>    ADVTXT.TXT     125   < 60>
SYSMAC.SML      42   < 60>    HELLO .MAC       1   < 60>
BOOT  .MAC      24   < 60>    

Total of 382 blocks in 13 files in SY:[11,70]

$ run advent

WELCOME TO ADVENTURE!!  WOULD YOU LIKE INSTRUCTIONS?

yes
SOMEWHERE NEARBY IS COLOSSAL CAVE, WHERE OTHERS HAVE FOUND FORTUNES IN
TREASURE AND GOLD, THOUGH IT IS RUMORED THAT SOME WHO ENTER ARE NEVER 
SEEN AGAIN.  MAGIC IS SAID TO WORK IN THE CAVE.  I WILL BE YOUR EYES
AND HANDS.  DIRECT ME WITH COMMANDS OF 1 OR 2 WORDS.  I SHOULD WARN 
YOU THAT I LOOK AT ONLY THE FIRST FOUR LETTERS OF EACH WORD, SO YOU'LL
HAVE TO ENTER "NORTHEAST" AS "NE" TO DISTINGUISH IT FROM "NORTH". 
(SHOULD YOU GET STUCK, TYPE "HELP" FOR SOME GENERAL HINTS.  FOR INFOR-
MATION ON HOW TO END YOUR ADVENTURE, ETC., TYPE "INFO".)
      - - - 
THIS PROGRAM WAS ORIGINALLY DEVELOPED BY WILLIE CROWTHER.  MOST OF THE
FEATURES OF THE CURRENT PROGRAM WERE ADDED BY DON WOODS (DON @ SU-AI).
THE CURRENT VERSION WAS DONE BY MIKE WESTON.

YOU ARE STANDING AT THE END OF A ROAD BEFORE A SMALL BRICK BUILDING.
AROUND YOU IS A FOREST.  A SMALL STREAM FLOWS OUT OF THE BUILDING AND 
DOWN A GULLY. 

quit
DO YOU REALLY WANT TO QUIT NOW? 

yes
OK

YOU SCORED  27 OUT OF A POSSIBLE 350, USING    8 TURNS.

YOU ARE OBVIOUSLY A RANK AMATEUR.  BETTER LUCK NEXT TIME. 

TO ACHIEVE THE NEXT HIGHER RATING, YOU NEED  9 MORE POINTS.
$ run $switch
Keyboard Monitor to switch to? basic

Ready

run acey
                          ACEY DUCEY CARD GAME
               CREATIVE COMPUTING  MORRISTOWN, NEW JERSEY


ACEY-DUCEY IS PLAYED IN THE FOLLOWING MANNER 
THE DEALER (COMPUTER) DEALS TWO CARDS FACE UP
YOU HAVE AN OPTION TO BET OR NOT BET DEPENDING
ON WHETHER OR NOT YOU FEEL THE CARD WILL HAVE
A VALUE BETWEEN THE FIRST TWO.
IF YOU DO NOT WANT TO BET, INPUT A 0
YOU NOW HAVE 100 DOLLARS.

HERE ARE YOUR NEXT TWO CARDS: 
 4 
 5 

WHAT IS YOUR BET? 1
 9 
SORRY, YOU LOSE
YOU NOW HAVE 99 DOLLARS.

HERE ARE YOUR NEXT TWO CARDS: 
 3 
QUEEN

WHAT IS YOUR BET? 1
QUEEN
SORRY, YOU LOSE
YOU NOW HAVE 98 DOLLARS.

HERE ARE YOUR NEXT TWO CARDS: 
 7 
JACK

WHAT IS YOUR BET? ^C

Ready

bye
Saved all disk files on SY: 416 blocks in use
Job 2 User 11,70 logged off KB0: at 31-Oct-86 11:14 AM
System RSTS V9.6-11 RSTS/E V9.6
Run time was 3.4 seconds
Elapsed time was 2 minutes
Good morning

Example boot of RSX 11M 4.6
PAUL NANKERVIS - PAULNANK@HOTMAIL.COM

BOOT> boot rp3


  RSX-11M V4.6 BL56   1912.K MAPPED
>RED DB3:=SY:
>RED DB3:=LB:
>MOU DB3:RSXM56
>@DB3:[1,2]STARTUP
>* PLEASE ENTER TIME AND DATE (HR:MN DD-MMM-YY) [S]: 11:12 31-OCT-76
>TIM 11:12 31-OCT-76
>* ENTER LINE WIDTH OF THIS TERMINAL [D D:132.]: 80
>SET /BUF=TI:80.
>ACS SY:/BLKS=1024.
>;
>; This system startup command file (LB:[1,2]STARTUP.CMD) contains a
>; template of commands to initialize the queue print spooler and queue
>; LP0:, initialize the error logger, initialize the DCL CLI, and install
>; the RMS Library and Utilities.  As is these commands are commented out
>; and are not executed.  To include these commands as part of the
>; startup procedure, edit the file to remove the period and semi-colon
>; (.;) comment delimiter from the beginning of each line.  These
>; commands may be useful for initializing the various facilities for
>; your installation or else they may provide a model with which to
>; tailor initialization commands for your particular installation. 
>;
>ELI /LOG
11:12:04  ERRLOG -- Error Logging initialized
>CLI /INIT=DCL/TASK=...DCL
>INS LB:[1,54]PIP.TSK
>INS LB:[1,54]EDT.TSK
>INS LB:[1,54]TKB.TSK
>INS LB:[1,54]MAC.TSK
>INS LB:[1,54]BRU.TSK
>@ <EOF>
>PIP [200,1]/LI


Directory DB3:[200,1]
31-OCT-76 11:12

GSA.MAC;1           19.        03-JAN-90 17:07
SEARCH.MAC;1        10.        03-JAN-90 17:07
RENAME.MAC;1        12.        03-JAN-90 17:07
ERASE.MAC;1         10.        03-JAN-90 17:07
PARSE.MAC;1         11.        03-JAN-90 17:07
SEARCH.TSK;1        26.     C  03-JAN-90 17:07
RENAME.TSK;1        26.     C  03-JAN-90 17:07
ERASE.TSK;1         25.     C  03-JAN-90 17:07
PARSE.TSK;1         22.     C  03-JAN-90 17:07
INTRO.ULB;1         199.       31-OCT-76 06:50
INTROFIL.CMD;1      2.         31-OCT-76 06:50
CLEAN.CMD;1         1.         31-OCT-76 06:50
CLKGEN.CMD;1        8.         31-OCT-76 06:50
DELETE.CMD;1        1.         31-OCT-76 06:50
LOGIN.CMD;1         1.         31-OCT-76 06:50
MYDISK.CMD;1        4.         31-OCT-76 06:50
SHAVE.CMD;1         1.         31-OCT-76 06:50
SHOW.CMD;1          1.         31-OCT-76 06:50
CLOCK.MAC;1         47.        31-OCT-76 06:50
HIYA.MAC;1          8.         31-OCT-76 06:50
STARS.MAC;1         2.         31-OCT-76 06:50
TMCLI.MAC;1         22.        31-OCT-76 06:50
TMCLI.FTN;1         22.        31-OCT-76 06:50
ERROR.TSK;1         4.      C  31-OCT-76 06:50
SEVERE.TSK;1        4.      C  31-OCT-76 06:50
SUCCESS.TSK;1       4.      C  31-OCT-76 06:50
WARNING.TSK;1       4.      C  31-OCT-76 06:50
FLU.TXT;1           1.         31-OCT-76 06:50
FLY.TXT;1           1.         31-OCT-76 06:50
FLY.TXT;2           1.         31-OCT-76 06:50
FLY.TXT;3           1.         31-OCT-76 06:50
HELLO.TXT;1         2.         31-OCT-76 06:50
LONG.TXT;1          25.        31-OCT-76 06:50
WHATSHERE.TXT;1     6.         31-OCT-76 06:50

Total of 533./533. blocks in 34. files

>HELP
 
        Help is available for many RSX-11M commands and utilities.  
 
        For help in logging into the system, type HELP HELLO or HELP 
        LOGIN.  You'll need a user-ID and password to log in.  Ask your
        system manager.
 
        RSX-11M systems have two major command languages or CLIs:  MCR
        and DCL.  Once you log in, your terminal is set to either
        MCR or DCL.  (All terminals are set to MCR prior to logging in.)
 
        The general forms of the HELP command are:
 
                >HELP[/cli] topic [subtopic[s]]
 
                >HELP commandname [switch]
 
        Once you are logged in, you need not include the name of the CLI
        to which your terminal is set.   
        
        For information on what further help is available, type
        HELP[/MCR] LIST (brackets indicate an optional command line
        entry) or HELP/DCL.  For a listing of help available on other
        topics, type HELP[/MCR] MORE or HELP/DCL MORE.  You need not
        log in to get help.
 
>TAS
LDR... 13.02  LDRPAR 248. 00002600 LB0:-00104402 FIXED
TKTN   05.00  SYSPAR 248. 00011700 LB0:-00110145 
...RMD 03.00  GEN    225. 00027200 LB0:-00112034 
F11MSG 13.00  GEN    200. 00005700 LB0:-00110164 
MTAACP 15.01  GEN    200. 00014700 LB0:-00111651 
...DMO 04.00  GEN    160. 00014600 LB0:-00107227 
MCR... 07.00  SYSPAR 160. 00011700 LB0:-00110050 
...DCL 5.04   GEN    160. 00051500 LB0:-00110525 
...MOU 27.01  GEN    160. 00037700 LB0:-00110402 
...MCR 07.00  GEN    160. 00020000 LB0:-00110211 
F11ACP 06.01  FCPPAR 149. 00024200 LB0:-00107323 
ERRLOG 2.00   GEN    148. 00040000 LB0:-00112507 
PMT... 2.00   GEN    148. 00006300 LB0:-00107503 
COT... 2.0    GEN    145. 00013600 LB0:-00107246 
PMD... 08.01  GEN    140. 00016200 LB0:-00111623 
SHF... 6.00   SYSPAR 105. 00011700 LB0:-00112174 
...INS 9.01   GEN    100. 00034600 LB0:-00107777 
...SAV 05.00  GEN    100. 00033300 LB0:-00111541 
...UFD 05.00  GEN    100. 00005700 LB0:-00110176 
QMG... 03.04  GEN     75. 00031700 LB0:-00110460 
PRT... 2.0    GEN     70. 00001100 LB0:-00110160 
LP0    06.00  GEN     70. 00014500 LB0:-00111437 
...ACS 3.00   GEN     70. 00005000 LB0:-00112500 
...BRU 11.03  GEN     70. 00173500 LB0:-00113217 
...EDT V03.17 GEN     65. 00145600 LB0:-00114674 
...AT. 9.0    GEN     64. 00060000 LB0:-00107575 
...QUE 05.01  GEN     50. 00020100 LB0:-00111244 
...PRI 05.01  GEN     50. 00020100 LB0:-00111244 
...BOO 06.02  GEN     50. 00022000 LB0:-00107166 
...ELI 1.00   GEN     50. 00017300 LB0:-00112553 
...MAG 03.00  GEN     50. 00031500 LB0:-00110077 
...LOA 04.02  GEN     50. 00032600 LB0:-00111777 
...HEL 04.00  GEN     50. 00024100 LB0:-00112354 
...BYE 07.00  GEN     50. 00012700 LB0:-00112312 
...BRO 07.00  GEN     50. 00030400 LB0:-00112421 
...UNL 4.02   GEN     50. 00024500 LB0:-00111406 
...PIP 18.03  GEN     50. 00040000 LB0:-00116314 
...TKB X43.00 GEN     50. 00070000 LB0:-00117002 
...MAC V05.05 GEN     50. 00070000 LB0:-00116130 
>BYE
Have a Good Morning
31-OCT-76 11:12 TT0:  logged off VIXEN 
>HELLO
Account or name: 11,70
Password: PDP

RSX-11M BL56   [1,54] System     VIXEN 
31-OCT-76 11:12 Logged on Terminal TT0:

Good Morning

>@LOGIN.CMD
        
                
Welcome to RSX-11M V4.6 time sharing

This is a minimal system with a user account of 200,1 (no password) and 11,70 (p
assword of PDP)

Hopefully it demonstrates how things were in the good old days

Paul Nankervis
paulnank@hotmail.com
http://skn.noip.me/pdp11/pdp11.html

>@ <EOF>
>DIR


Directory DB3:[11,70]
31-OCT-76 11:12

HELLO.MAC;1         1.         31-OCT-76 06:55
LOGIN.CMD;1         1.         31-OCT-76 06:48
LOGIN.CMD;2         1.         31-OCT-76 06:48

Total of 3./3. blocks in 3. files

>EDIT HELLO.MAC
    1       ;       HELLO WORLD IN ASSEMBLER FOR THE DEC PDP-11 WITH THE
*type whole
    1       ;       HELLO WORLD IN ASSEMBLER FOR THE DEC PDP-11 WITH THE
    2       ;   RSX-11M-PLUS OPERATING SYSTEM
    3       ;
    4               .TITLE HELLO
    5               .IDENT /V0001A/
    6               .MCALL QIOW$S, EXIT$S
    7               .PSECT $CODE,RO,I
    8       START:  QIOW$S  #IO.WVB,#5,#2,,,,<#STR,#LEN,#40>
    9               EXIT$S
   10               .PSECT $DATA,RO,D
   11       STR:    .ASCII / HELLO WORLD!/
   12               LEN=.-STR
   13               .END START
   14           
[EOB]
*exit
DB3:[11,70]HELLO.MAC;2 14 lines
>MACRO HELLO
>LINK HELLO
>RUN HELLO
 HELLO WORLD!
>LOGOUT
Have a Good Morning
31-OCT-76 11:13 TT0:  logged off VIXEN 
>

