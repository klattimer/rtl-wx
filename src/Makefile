VERSION_STRING = v3.2
BUILD_DATE_STRING=$(shell date +"%m-%d-%Y")

# Compile flag to activate Efergy energy sensor support.  These sensors
# require rtl_fm code to re-process all rtl-sdr data,  which means extra cpu load.
# On a raspberry pi, enabling this flag causes cpu load to jump from 10% to 20%
EFERGY_SUPPORT_FLAG = -D ENABLE_EFERGY_SUPPORT

# Uncomment below to build for Netgear R7000 or similar cpu on DD-WRT
# Must be compiled with cross tools (eg hosted on Ubuntu)  
# Target cpu for R7000 is ARMv7 Cortex-A9
# Requires wrt cross toolchain:
#    eg. OpenWrt-Toolchain-bcm53xx-for-arm_cortex-a9-gcc-4.8-linaro_uClibc-0.9.33.2_eabi
# Once you have the toolchain tar, expand it somewhere and set vars below to point to gcc and lib
TARGET_DISPLAY_NAME = Netgear R7000
#TOOLCHAIN=/home/ubuntu/toolchain-arm_cortex-a9_gcc-4.8-linaro_uClibc-0.9.33.2_eabi
export STAGING_DIR = . 
#CC   = $(TOOLCHAIN)/bin/arm-openwrt-linux-uclibcgnueabi-gcc
CFLAGS = -I. -I/usr/include/libusb-1.0 -Ilibrtlsdr/include $(EFERGY_SUPPORT_FLAG)
LIBUSB_DIR = /usr/lib
LIBRTLSDR_DIR = librtlsdr/build/src

# Uncomment Below to build for Raspberry Pi (native compile)
#TARGET_DISPLAY_NAME = Raspberry Pi
#CC = gcc
#CFLAGS = -I. -I/usr/include/libusb-1.0 -Ilibrtlsdr/include
#CFLAGS = -I. -I/usr/include/libusb-1.0 -Ilibrtlsdr/include $(EFERGY_SUPPORT_FLAG)
#LIBUSB_DIR = .
#LIBRTLSDR_DIR = librtlsdr/build/src

# Netgear WNR3500L running DD-WRT using cross tools on hosted on
# linux.  Target cpu is MIPSEL Requires cross toolchain, 
#   eg. toolchain-mipsel_gcc4.1.2
# NOTE: NOT WORKING - Program had error opening USB device, suspect libusb-1.0
#TARGET_DISPLAY_NAME = WNR3500L
#TOOLCHAIN=/home/ubuntu/toolchain-mipsel_gcc4.1.2
#export STAGING_DIR = . 
#CC   = $(TOOLCHAIN)/bin/mipsel-linux-gcc
#CFLAGS = -I. -I$(TOOLCHAIN)/include/libusb-1.0 -Ilibrtlsdr/include
#LIBUSB_DIR = $(TOOLCHAIN)/lib
#LIBRTLSDR_DIR = librtlsdr/build/src

ODIR=obj

DEPS = rtl-wx.h TagProc.h getopt.h

_RTLWX_OBJ = rtl-wx.o TagProc.o DataStore.o ConfProc.o Scheduler.o Util.o rtl-433fm-demod.o rtl-433fm-decode.o getopt.o
RTLWX_OBJ = $(patsubst %,$(ODIR)/%,$(_RTLWX_OBJ))

_RTL433_OBJ = rtl-433fm-standalone.o rtl-433fm-demod.o rtl-433fm-decode.o getopt.o 
RTL433_OBJ = $(patsubst %,$(ODIR)/%,$(_RTL433_OBJ))

SPACE_CHAR :=
SPACE_CHAR +=
TARGET_DISPLAY_NAME_NO_SPACES = $(subst $(SPACE_CHAR),,$(TARGET_DISPLAY_NAME))

$(ODIR)/%.o: %.c $(DEPS)  | ../bin/$(TARGET_DISPLAY_NAME_NO_SPACES) obj
	$(CC) -O3 -c -o $@ $< $(CFLAGS)

all: rtl-wx rtl-433fm

obj:
	mkdir obj

../bin/$(TARGET_DISPLAY_NAME_NO_SPACES):
	mkdir ../bin/$(TARGET_DISPLAY_NAME_NO_SPACES)

rtl-wx: $(RTLWX_OBJ)
	$(CC)  -O3 -Wall -Wextra -Wno-unused -Wsign-compare -g3 -fvisibility=hidden -o $@ $^ -Wl,-Bstatic -lconvenience_static -lrtlsdr -Wl,-Bdynamic -lpthread -lusb-1.0 -lm -L$(LIBUSB_DIR) -L$(LIBRTLSDR_DIR)
	sed -e 's/INSERT_BUILD_STRING/$(VERSION_STRING) built on $(BUILD_DATE_STRING)/g' -e 's/INSERT_TARGET_STRING/for $(TARGET_DISPLAY_NAME)/g' ../www/misc/header.in > ../www/misc/header.htm
	cp ../www/misc/header.htm ../bin/$(TARGET_DISPLAY_NAME_NO_SPACES)/header.htm
	cp rtl-wx ../bin/$(TARGET_DISPLAY_NAME_NO_SPACES)/rtl-wx
	cp rtl-wx ../bin/rtl-wx

rtl-433fm: $(RTL433_OBJ)
	$(CC)   -O3 -Wall -Wextra -Wno-unused -Wsign-compare -g3 -fvisibility=hidden -o $@ $^ -Wl,-Bstatic -lconvenience_static -lrtlsdr -Wl,-Bdynamic -lpthread -lusb-1.0 -lm -L$(LIBUSB_DIR) -L$(LIBRTLSDR_DIR)
	cp rtl-433fm ../bin/$(TARGET_DISPLAY_NAME_NO_SPACES)/rtl-433fm
	cp rtl-433fm ../bin/rtl-433fm

.PHONY: clean

clean:
	rm -f $(ODIR)/*.o

