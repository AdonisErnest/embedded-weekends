# 00 – Development environment setup

Ubuntu Server (ARM64) in a UTM virtual machine on a MacBook Pro (M4 Pro), used to build
and flash firmware for an Arduino Uno (ATmega328P) entirely from the Linux command line.

## VM settings
- UTM, QEMU backend (Apple Virtualization off, so the Uno can be passed through over USB)
- 4 GB RAM, 4 cores, 32 GB disk, Ubuntu Server ARM64 with OpenSSH
- Worked over SSH from the macOS Terminal

## Toolchain
- avr-gcc, avr-libc, avrdude, make (apt)
- arduino-cli 1.6.0 with the arduino:avr core
- Python 3 venv with pyserial and matplotlib

## Problems I hit and how I fixed them
- **Arduino IDE on macOS failed with "bad CPU type in executable."** Its AVR compiler is an
  Intel build. Instead of installing Rosetta, I moved compiling and uploading into Ubuntu
  with arduino-cli, which has a native ARM64 build.
- **arduino-cli "not found" after install.** It installs to ~/bin, which wasn't on PATH.
  Added `export PATH="$HOME/bin:$PATH"` to ~/.bashrc and reloaded it with `source`.
- **"signature expired: is your system clock set correctly?"** The VM clock was about 21
  hours behind after the Mac slept. systemd-timesyncd isn't on this Ubuntu release, so I
  set the clock from an HTTPS Date header with `date -s`.
- **Uno passthrough:** UTM's USB menu (QEMU only) hands the board to the VM; it shows up
  as /dev/ttyACM0. Added my user to the dialout group for serial access.

## Verified
- AnalogReadSerial compiled and uploaded with arduino-cli; readings streamed into
  Ubuntu with pyserial's miniterm.
