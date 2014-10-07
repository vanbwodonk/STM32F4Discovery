PROJECT=GPIO
TARGET=main
EXECUTABLE=main.elf
MCU = cortex-m4
OPTIMIZE = -Os
CLI_PATH = C:\Program Files\STMicroelectronics\STM32 ST-LINK Utility\ST-LINK Utility\ST-LINK_CLI

CROSS_COMPILE ?= arm-none-eabi-

CC=$(CROSS_COMPILE)gcc
LD=$(CROSS_COMPILE)gcc
AR=$(CROSS_COMPILE)ar
AS=$(CROSS_COMPILE)as
CP=$(CROSS_COMPILE)objcopy
OD=$(CROSS_COMPILE)objdump
SIZE=$(CROSS_COMPILE)size

BIN =$(CP) -O binary 

DEFS = -DUSE_STDPERIPH_DRIVER  
DEFS += -DSTM32F4XX
DEFS += -DHSE_VALUE=8000000

MCFLAGS = -mcpu=$(MCU)
MCFLAGS += -Wall 
MCFLAGS += -mthumb 
MCFLAGS += -ffunction-sections 
MCFLAGS += -fdata-sections 
MCFLAGS += -fmessage-length=0 
MCFLAGS += -D__ASSEMBLY__ 
MCFLAGS += -mlittle-endian
MCFLAGS += -mfpu=fpv4-sp-d16
MCFLAGS += -mfloat-abi=hard
MCFLAGS += -mthumb-interwork
MCFLAGS += -std=gnu99
MCFLAGS += --specs=nano.specs

LDFLAGS  = -nostartfiles 
LDFLAGS += -Wl,--gc-sections 
LDFLAGS += -Xlinker -Map=$(TARGET).map

STM32_INCLUDES = -I../$(PROJECT) \
	-I../../Libraries/CMSIS/ST/STM32F4xx/Include/ \
	-I../../Libraries/CMSIS/Include/ \
	-I../../Libraries/STM32F4xx_StdPeriph_Driver/inc/ \
	-I../../Utilities/STM32F4-Discovery \
	-I../../Libraries/STM32_USB_Device_Library/Class/hid/inc \
	-I../../Libraries/STM32_USB_Device_Library/Core/inc/ \
	-I../../Libraries/STM32_USB_OTG_Driver/inc/

STM32_USB_OTG_SRC = ../../Libraries/STM32_USB_OTG_Driver/src/usb_dcd_int.c \
	../../Libraries/STM32_USB_OTG_Driver/src/usb_core.c \
	../../Libraries/STM32_USB_OTG_Driver/src/usb_dcd.c

STM32_USB_DEVICE_SRC = ../../Libraries/STM32_USB_Device_Library/Class/hid/src/usbd_hid_core.c \
	../../Libraries/STM32_USB_Device_Library/Core/src/usbd_req.c \
	../../Libraries/STM32_USB_Device_Library/Core/src/usbd_core.c \
	../../Libraries/STM32_USB_Device_Library/Core/src/usbd_ioreq.c

SRC = main.c \
	../../Utilities/STM32F4-Discovery/system_stm32f4xx.c \
	../../Libraries/STM32F4xx_StdPeriph_Driver/src/stm32f4xx_gpio.c \
	../../Libraries/STM32F4xx_StdPeriph_Driver/src/stm32f4xx_rcc.c \
	../../Libraries/STM32F4xx_StdPeriph_Driver/src/stm32f4xx_syscfg.c \
	../../Libraries/STM32F4xx_StdPeriph_Driver/src/misc.c \
	../../Libraries/STM32F4xx_StdPeriph_Driver/src/stm32f4xx_exti.c \
	../../Libraries/STM32F4xx_StdPeriph_Driver/src/stm32f4xx_usart.c \
	../../Libraries/STM32F4xx_StdPeriph_Driver/src/stm32f4xx_i2c.c \
	../../Utilities/STM32F4-Discovery/stm32f4_discovery.c \
	../../Libraries/STM32F4xx_StdPeriph_Driver/src/stm32f4xx_spi.c 

CFLAGS	= $(MCFLAGS)  $(LDFLAGS)  $(OPTIMIZE)  $(DEFS) -I./ -I./ $(STM32_INCLUDES)  -Wl,-T,stm32_flash.ld
AFLAGS	= $(MCFLAGS) 

STARTUP = startup_stm32f4xx.S

OBJDIR = .
OBJ = $(SRC:%.c=$(OBJDIR)/%.o) 
OBJ += Startup.o

all: $(TARGET) size

$(TARGET): $(EXECUTABLE)
	$(BIN) $^ $@.bin

$(EXECUTABLE): $(SRC) $(STARTUP)
	$(CC) $(CFLAGS) $^ -lm -lc -lnosys -o $@
	
size:
	-@echo ""
	@$(SIZE) $(EXECUTABLE)
	
burn:
	$(CLI_PATH) -c SWD -q -p ${TARGET}.bin 0x08000000 -V -Rst

clean:
	rm -f Startup.lst  $(TARGET)  $(TARGET).lst $(OBJ) $(AUTOGEN)  $(TARGET).out  $(TARGET).hex  $(TARGET).bin  $(TARGET).map  $(TARGET).dmp  $(TARGET).elf
	
