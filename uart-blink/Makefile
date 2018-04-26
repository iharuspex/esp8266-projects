PROJ_NAME=uart-blink
COMPORT=/dev/ttyUSB0
VPATH=.:driver .:xprintf
OBJS=user_main.o uart.o xprintf.o
CC=xtensa-lx106-elf-gcc
ESPTOOL=~/Projects/esp-open-sdk/esptool/esptool.py
ESP8266_SDK_ROOT=~/Projects/esp-open-sdk/sdk
CCFLAGS= --std=c99 -Wimplicit-function-declaration -fno-inline-functions -mlongcalls -mtext-section-literals \
         -mno-serialize-volatile -I $(ESP8266_SDK_ROOT)/include -I. -I ./include -D__ETS__ -DICACHE_FLASH -DXTENSA -DUSE_US_TIMER -DDEBUG
LDFLAGS=-nostdlib \
        -L $(ESP8266_SDK_ROOT)/lib -L $(ESP8266_SDK_ROOT)/ld -T $(ESP8266_SDK_ROOT)/ld/eagle.app.v6.ld \
        -Wl,--no-check-sections -u call_user_start -Wl,-static -Wl,--start-group \
        -lc -lgcc -lhal -lphy -lpp -lnet80211 -llwip -lwpa -lmain -ljson -lupgrade -lssl \
        -lpwm -lsmartconfig -Wl,--end-group

all: $(PROJ_NAME)_0x00000.bin

$(PROJ_NAME).out: $(OBJS)
	@echo "[LD] $@"
	@$(CC) -o $(PROJ_NAME).out $(LDFLAGS) $(OBJS)
	@echo "Invoking: Xtensa lx106 size..."
	@xtensa-lx106-elf-size $@ 
	

$(PROJ_NAME)_0x00000.bin: $(PROJ_NAME).out
	@echo "Generate image..."
	@python2.7 $(ESPTOOL) elf2image --output ${PROJ_NAME}- $^

.c.o:
	@echo "[CC] $<"
	@$(CC) $(CCFLAGS) -c $<

clean:
	rm -f $(PROJ_NAME).out *.o *.bin

BLANK = $(ESP8266_SDK_ROOT)/bin/blank.bin
INIT_DATA = $(ESP8266_SDK_ROOT)/bin/esp_init_data_default.bin

flash: all
	python2.7 $(ESPTOOL) --port $(COMPORT) write_flash 0x00000 $(PROJ_NAME)-0x00000.bin 0x10000 $(PROJ_NAME)-0x10000.bin 0x7B000 $(BLANK) 0x7C000 $(INIT_DATA) 0x7E000 $(BLANK)
