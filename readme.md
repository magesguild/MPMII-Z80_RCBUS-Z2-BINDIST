**MP/M II RCBus Z80 Binary Distribution**

This is a collection of binary files used to spin up a working MP/M II
installation based on Tadeusz Pycio's contributions to the platform. It includes
both necessary and optional files for the platform from multiple projects. This
build is tested using a custom system running the Z80Kits (RC2014 official) 512k
ROM 512k RAM module, located here:

- https://z80kits.com/shop/512k-rom-512k-ram-module/

The official RC2014 ZED and ZED PRO builds include this ROM/RAM module, though
the backplanes included do not include the jumpers necessary to daisy-chain
the interrupt signals required for this build. They also do not include the CTC
modules required to implement interrupt signals used in queued console support.

**Sources**
- MPMII-RCBus: https://github.com/tpycio/MPMII-RCBus
- Extras: Various tools from Kevin Boone: https://github.com/kevinboone

**Hardware Supported**

Besides the ROM/RAM modules linked to above, this build needs the following:

- A backplane supporting daisy chaining, such as the SC702 or similar:
    - https://smallcomputercentral.com/rcbus/sc700-series/sc702-rcbus-backplane/
Note: the daisy chain must be set up between the CTC module, the SIO module, and
the CPU module. No other daisy chain configuration is required, and may cause
issues if configured.
- A CF Card Module, such as the SC729, SC715, or those included with the ZED systems:
    - https://smallcomputercentral.com/rcbus/sc700-series/sc729-rcbus-compact-flash-module/
    - https://smallcomputercentral.com/rcbus/sc700-series/sc715-rcbus-compact-flash-module/
    - https://z80kits.com/shop/compact-flash-module/
- A suitably configurable CTC module, such as the SC716:
    - https://smallcomputercentral.com/rcbus/sc700-series/sc716-rcbus-z80-sio-2-serial-module/

**About the CTC**
Note: Other CTC modules may work, but only the SC716 has been tested. Set the jumpers as follows:       
- JP3 - pins 1-2
- JP6 - pins 3-4
- P2 (not JP2, P2 is on the right side) - pins 5-6
- Also you should probably enable JP7-JP10, though I am unsure if this is strictly required.

The author of MP/M II for RCBus has recommended putting the SIO module into a higher priority than
the CTC module in the daisy chain (priority ordering is indicated on SCC backplane silkscreens.)

**Kernel Configuration**

The Kernel is built with the following settings:

	MP/M II V2.1 System Generation
	Copyright (C) 1981, Digital Research


	Default entries are shown in (parens).
	Default base is Hex, precede entry with # for decimal

	Use SYSTEM.DAT for defaults (Y) ?
	Top page of operating system (FF) ?
	Number of TMPs (system consoles) (#2) ?
	Number of Printers (#1) ?
	Breakpoint RST (06) ?
	Enable Compatibility Attributes (Y) ?
	Add system call user stacks (Y) ?
	Z80 CPU (Y) ?
	Number of ticks/second (#50) ?
	System Drive (A:) ?
	Temporary file drive (P:) ?
	Maximum locked records/process (#16) ?
	Total locked records/system (#32) ?
	Maximum open files/process (#153) ?
	Total open files/system (#153) ?
	Bank switched memory (Y) ?
	Number of user memory segments (#7) ?
	Common memory base page (C0) ?
	Dayfile logging at console (Y) ?

	 SYSTEM  DAT  FF00H  0100H
	 TMPD    DAT  FE00H  0100H
	 USERSYS STK  FC00H  0200H
	 XIOSJMP TBL  FB00H  0100H

	Accept new system data page entries (Y) ?

	 RESBDOS SPR  EF00H  0C00H
	 XDOS    SPR  CD00H  2200H
	 BNKXIOS SPR  B300H  1A00H
	 BNKBDOS SPR  9000H  2300H
	 BNKXDOS SPR  8E00H  0200H
	 TMP     SPR  8A00H  0400H

	 LCKLSTS DAT  8200H  0800H
	 CONSOLE DAT  8000H  0200H

	Enter memory segment table:
	 Base,size,attrib,bank (80,80,80,00) ?
	 Base,size,attrib,bank (00,C0,00,01) ?
	 Base,size,attrib,bank (00,C0,00,02) ?
	 Base,size,attrib,bank (00,C0,00,03) ?
	 Base,size,attrib,bank (00,C0,00,04) ?
	 Base,size,attrib,bank (00,C0,00,05) ?
	 Base,size,attrib,bank (00,C0,00,06) ?
	 Base,size,attrib,bank (00,C0,00,07) ?

	 MP/M II Sys  8000H  8000H  Bank 00
	 Memseg  Usr  0000H  C000H  Bank 01
	 Memseg  Usr  0000H  C000H  Bank 02
	 Memseg  Usr  0000H  C000H  Bank 03
	 Memseg  Usr  0000H  C000H  Bank 04
	 Memseg  Usr  0000H  C000H  Bank 05
	 Memseg  Usr  0000H  C000H  Bank 06
	 Memseg  Usr  0000H  C000H  Bank 07

	Accept new memory segment table entries (Y) ?

	** GENSYS DONE **

**Installation**

You will need to use the SCM build provided in the scm directory named
'ROM\_SCM\_131\_Z80\_P3.bin'

Use minipro and your chip programmer to flash this to your memory module.

From here you can install CP/M 2 using the contents of the scm\_apps directory.
Unlike the z180 revision, no DOWNLOAD.COM nor pkgFiles for use with DOWNLOAD.COM
are included. This is because I had issues actually getting he tool working on
this build of the system. However, MBASIC.COM and NULU.COM are provided in the
standalone directory. CPMTOOLS.LBR in the lbr directory covers additional CP/M
specific tools.

For this build, Hardware Flow Control must be enabled in your minicom (or equiv-
alent) or you will receive transmission failures. Upon booting into MP/M II, you
need to disable Hardware Flow Control, or your input will lock up entirely. If
your system locks up after running MPM.COM, simply disable Hardware Flow Control
and your input will begin workig again.

This is the minimum set of files you need:

- KRNZ80.LBR
- KRNLDR80.LBR
    - note that you only _need_ mpm.sys from this library, the rest are there
      for examination/troubleshooting.
- DISTRIB.LBR

The other files are optional, but you probably want to install Y2KTOOLS.LBR
_after_ you install DISTRIB.LBR. This will enable Y2K compliance on your system.
This package was kept separate for those who want historical accuracy, including
Y2K non-compliance.

You may also want ADDON80.LBR for extended operating system functions.

CPMTOOLS.LBR contains a wide range of CP/M 2 command, including multiple
assemblers and the Aztec C compile toolchain from Manx Software. These are
optional but highly recommended. You may want to install them in specific places
or just install them directly to the default A: drive.

EXTRAS.LBR contains various tools created by Kevin Boone for CP/M 2. I recommend
scalc and kcalc, in particular. Some tools are unixy, which is fun.

Use NULU.COM to extract LBR files. You can extract files selectively if you only
want certain tools from CPMTOOLS.LBR or EXTRAS.LBR.

**Documentation**

The doc dir contains a range of documentation and, while it is far from
complete, it should be enough to get any user developing in assembly and C with
some confidence. Some of the assemblers provided in the CPMTOOLS.LBR have been
difficult to find documentation for, but I've provided as many as I have been
able to scrape together (so far, a deeper search may yield more documentation in
the future.)

**System Administration**

The SET.PRL command is extremely important for administering the system. While
it may be a bit silly to be worried about admin versus non-admin users and their
permission sets on these particular systems, where any user with physical access
can simply boot into CP/M and gain full admin access, MP/M II gives us tools for
managing file access, and there are some considerations to be made if you do
want commands and files protected.

SET.PRL itself can disable all password protection on any given drive, so it
must, itself, be password protected for this to work well. Please read through
the SET command documentation provided in the MP/M II User Guide closely before
spending too much time attempting to password protect files.

If you password protect SET, you need to give it the password manually like this
to use it (to set your default password, which can then unlock things
automatically):

    set;s3kr1t [default = s3kr1t]

Replace s3kr1t with your own password.

Also note that a password protected file must reside in a password enabled drive
to function as expected.

    A:set [protect = on]

Again, see the manual for more details, but this is a potential stumbling block,
so practice it if you want to use passwords.

This does mean that non administrative users can not password protect their own
files, though an administrators can use different passwords to protect files
from each other. It means all admins who need to use the set command must share
a password for that command. Bare these things in mind as you build out your
user design.

**Addons**

While the addons are included in the distribution, they are not built into the
kernel. In order to use them, extract the addons you are interested into the A:
drive and rebuild the kernel using GENSYS. You can select the defaults or change
anything you like, but you will be prompted to include the addons you've
unpacked here. 
