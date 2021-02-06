#******************************************************************************
# Copyright (C) 2017 by Alex Fosdick - University of Colorado
#
# Redistribution, modification or use of this software in source or binary
# forms is permitted as long as the files maintain this copyright. Users are 
# permitted to modify this and use it to learn about the field of embedded
# software. Alex Fosdick and the University of Colorado are not liable for any
# misuse of this material. 
#
#*****************************************************************************

#------------------------------------------------------------------------------
# Simple makefile for the msp432 board and host pc
#
# Use: make [TARGET] [PLATFORM-OVERRIDES]
#
# Build Targets:
#	   <FILE>.i        - Builds <FILE>.i preprocessed file
#	   <FILE>.asm      - Builds <FILE>.asm assembly file
#      <FILE>.o        - Builds <FILE>.o object file
#	   compile-all     - Builds <FILE1>.o <FILE2>.o all object files for the whole project
#      build           - Builds and links all source files
#      clean           - removes all generated files
#
# Platform Overrides:
#      PLATFORM=HOST   - Targeting the host environment
#	   PLATFORM=MSP432 - Targeting the MSP432 board 
#
#------------------------------------------------------------------------------

# Inclusion of sources.mk which includes all the needed source files and header files
include sources.mk

# TARGET is the output file name for the make build command which will produce c1m2.map c1m2.out 
TARGET = c1m2

# You sould provide the PLATFORM from the command line to determine 
# the Targeted platform is it MSP432 board or HOST
# While specifying the operation override the platform ex: make build PLATFORM=HOST or make build PLATFORM=MSP432
ifeq ($(PLATFORM),MSP432)

	# Architecture Specific Flags
	CPU = cortex-m4
	ARCH = thumb
	SPECS = nosys.specs
 
	# Platform Specific Flags
	PLATFORM = MSP432
	LINKER_FILE = ../msp432p401r.lds

	# Compile Defines
	CC = arm-none-eabi-gcc
	LDFLAGS = -Wl,-Map=$(TARGET).map -T $(LINKER_FILE)
	CFLAGS = -D$(PLATFORM) -mcpu=$(CPU) -m$(ARCH) --specs=$(SPECS) -Wall

else ifeq ($(PLATFORM),HOST)

	# Platform Specific Flags
	PLATFORM = HOST

	# Compile Defines
	CC = gcc
	LDFLAGS = -Wl,-Map=$(TARGET).map
	CFLAGS = -D$(PLATFORM) -Wall
	#overriding the Source and header files if the HOST PLATFORM is selected
	SOURCES = main.c \
		memory.c
	INCLUDES = -I ../include/common

endif

# Defining the object files by copy the names of the source file and change the extention to .o
OBJS = $(SOURCES:.c=.o)

# Rule To produce preprocessed file if needed
# Command: make filename.i PLATFORM=HOST or make filename.i PLATFORM=MSP432
%.i : %.c
	$(CC) -E $< $(CFLAGS) -o $@ $(INCLUDES)

# Rule To produce assembly file if needed
# Command: make filename.asm PLATFORM=HOST or make filename.asm PLATFORM=MSP432
%.asm : %.c
	$(CC) -S $< $(CFLAGS) -o $@ $(INCLUDES)

# Rule To produce object file if needed
# Command: make filename.o PLATFORM=HOST or make filename.o PLATFORM=MSP432
%.o : %.c
	$(CC) -c $< $(CFLAGS) -MD -o $@ $(INCLUDES)


# Rule To compile and produce object files for all source files
# Command: make compile-all PLATFORM=HOST or make compile-all PLATFORM=MSP432
.PHONY: compile-all
compile-all: $(OBJS)

# Rule To build the whole project and generate map file: $(TARGET).map and executable file: $(TARGET).out
# Command: make build PLATFORM=HOST or make build PLATFORM=MSP432
.PHONY: build
build: $(TARGET).out
$(TARGET).out: $(OBJS)
	$(CC) $(OBJS) $(CFLAGS) $(LDFLAGS) -o $@

# Rule To remove all the generated files 
# Command: make clean
.PHONY: clean
clean:
	rm -f $(OBJS) $(TARGET).out $(TARGET).map *.asm *.i *.d


