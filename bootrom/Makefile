# Makefile for ESP32 reverse engineering
# Tom Trebisky  5-12-2018
#
#   currently on my system the following
#   environment variable is set:
# IDF_PATH=/opt/esp32/esp-idf

all:
	@echo "You need to be more specific"

#PORT           = /dev/ttyUSB1
PORT           = /dev/ttyUSB2

#TOOL_PATH = /opt/esp-open-sdk/xtensa-lx106-elf/bin
#TOOL_PATH = /opt/esp32/xtensa-esp32-elf/bin

ESPTOOL = $(IDF_PATH)/components/esptool_py/esptool/esptool.py -p $(PORT)
ESPSYMDIR1 = $(IDF_PATH)/components/esp32/ld
ESPSYMDIR2 = $(IDF_PATH)/components/esptool_py/esptool/flasher_stub

# This can be found on our search path
#NOBJDUMP = xtensa-lx106-elf-objdump -D -b binary -mxtensa
NOBJDUMP = xtensa-esp32-elf-objdump -D -b binary -mxtensa
OBJDUMP = xtensa-esp32-elf-objdump -mxtensa -d -z 

dis:
	time ./espdis >espdis.out

info:
	$(ESPTOOL) read_mac
	$(ESPTOOL) flash_id

.PHONY: syms

# clump together all available symbol lists
# The files in the flasher_stub directory both add some
# additional names and change certain names
syms:
	cp $(ESPSYMDIR1)/esp32.rom.ld syms
	cat $(ESPSYMDIR1)/esp32.peripherals.ld >>syms
	cat $(ESPSYMDIR1)/esp32.rom.ld >>syms
	cat $(ESPSYMDIR1)/esp32.rom.nanofmt.ld >>syms
	cat $(ESPSYMDIR1)/esp32.rom.spiflash.ld >>syms
	cat $(ESPSYMDIR1)/esp32.rom.spiram_incompatible_fns.ld >>syms
	cat $(ESPSYMDIR2)/rom_32.ld >>syms

# this file adds nothing new
#	cat $(ESPSYMDIR2)/stub_32.ld >>syms

#ROM0_SIZE = 65536 * 5
ROM0_SIZE = 393216
ROM1_SIZE = 65536

esp32_rom0.bin:
	$(ESPTOOL) dump_mem 0x40000000 $(ROM0_SIZE) esp32_rom0.bin

esp32_rom1.bin:
	$(ESPTOOL) dump_mem 0x3ff90000 $(ROM1_SIZE) esp32_rom1.bin

esp32.bin:
	cat esp32_rom0.bin esp32_rom1.bin >esp32.bin

extra:
	$(ESPTOOL) dump_mem 0x40060000 65536 extra.bin

# This works, but is brute force and has many out of sync sections.
# Not only that, but the addresses are all at 0x0 not 0x40000000
#naive.dis:
#	$(OBJDUMP) esp32_rom0.bin >naive.dis

# This is better, at least the addresses are right
# but it is still virtually useless.
# We start at the ROM reset address so at least that
# first part of the disassembly comes out right.
naive.dis:
	$(OBJDUMP) --start-address=0x40000400 esp32.elf >naive.dis

esp32.elf:    wrap esp32.bin
	./wrap esp32.bin esp32.elf

wrap:	wrap.c
	cc -o wrap wrap.c

# from "thing"
both:
	$(ESPTOOL) dump_mem 0x40000000 $(ROM0_SIZE) xesp32_rom0.bin
	$(ESPTOOL) dump_mem 0x3ff90000 $(ROM1_SIZE) xesp32_rom1.bin

