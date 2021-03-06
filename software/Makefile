PORT = /dev/cu.wchusbserial1410

CC = avr-gcc
CXX = avr-g++
#CC = /Applications/Arduino.app/Contents/Java/hardware/tools/avr/bin/avr-gcc
#CXX = /Applications/Arduino.app/Contents/Java/hardware/tools/avr/bin/avr-g++
HARDWARE = /Applications/Arduino.app/Contents/Java/hardware
OBJDIR = obj
DEFINES = -DF_CPU=16000000L -DARDUINO=10605 -DARDUINO_AVR_NANO -DARDUINO_ARCH_AVR

MICRO = -mmcu=atmega328p
CFLAGS = -std=gnu11 $(MICRO) $(DEFINES) -g -Os -fno-exceptions -ffunction-sections -fdata-sections -fno-threadsafe-statics -MMD
CXXFLAGS = -std=gnu++11 $(MICRO) $(DEFINES) -g -Os -fno-exceptions -ffunction-sections -fdata-sections -fno-threadsafe-statics -MMD
ELFFLAGS = -w -Os -flto -fuse-linker-plugin -Wl,--gc-sections,--relax -mmcu=atmega328p

INCLUDE += -I$(HARDWARE)/arduino/avr/cores/arduino
INCLUDE += -I$(HARDWARE)/arduino/avr/variants/eightanaloginputs
INCLUDE += -I$(HARDWARE)/arduino/avr/libraries/Wire
INCLUDE += -I$(HARDWARE)/arduino/avr/libraries/Wire/utility
INCLUDE += -I$(HARDWARE)/arduino/avr/libraries/SPI
INCLUDE += -I SD/
INCLUDE += -I SD/utility
INCLUDE += -I rlcs_upstream/src/
INCLUDE += -I arduino-ms5xxx/src/
INCLUDE += -I include/

VPATH += .
VPATH += rlcs_upstream/src/
VPATH += arduino-ms5xxx/src/
VPATH += SD/
VPATH += SD/utility
VPATH += $(HARDWARE)/arduino/avr/cores/arduino/
VPATH += $(HARDWARE)/arduino/cores/arduino/
VPATH += $(HARDWARE)/arduino/avr/libraries/Wire
VPATH += $(HARDWARE)/arduino/avr/libraries/Wire/utility
VPATH += $(HARDWARE)/arduino/avr/libraries/SPI


# Project source
default: build upload

upload:
	avrdude -V -F -p m328p -c arduino -b57600 -Uflash:w:main.hex -P$(PORT)

build: main.hex

main.hex: main.elf
	avr-objcopy -O ihex $< $@

#our source files
OBJECTS = $(addprefix $(OBJDIR)/, main.o nodeio.ioio.o IMU.o gyro.o MS5xxx.o sdlogger.o)
main.elf: $(OBJECTS) lib
	$(CC) $(ELFFLAGS) -o $@ $(OBJECTS) libarduino.a -lm

# Arduino library compilation
# revise whether I need all of these
LIB_OBJ = $(addprefix $(OBJDIR)/, wiring_pulse.o WInterrupts.o hooks.o wiring.o wiring_analog.o \
	wiring_digital.o wiring_shift.o CDC.o HardwareSerial.o HardwareSerial0.o \
	HardwareSerial1.o HardwareSerial2.o HardwareSerial3.o IPAddress.o \
	Print.o Stream.o Tone.o USBCore.o WMath.o WString.o abi.o Wire.o twi.o \
	SPI.o SD.o File.o Sd2Card.o SdFile.o SdVolume.o)

lib: $(LIB_OBJ)
	-rm libarduino.a
	avr-ar rcs libarduino.a $^


$(OBJDIR) obj/%.o: %.c
	$(CC) $(CFLAGS) $(NODE) $(INCLUDE) -c $< -o $@

$(OBJDIR) obj/%.o: %.cpp
	$(CXX) $(CXXFLAGS) $(NODE) $(INCLUDE) -c $< -o $@

clean:
	-rm obj/*
	-rm *.hex
	-rm *.elf
	-rm libarduino.a

