#
# A simple makefile for building program composed of C/asm source files.
# Builds for ARM target using cross-development toolchain. 
#

# Configure compile options via CFLAGS
#  -g        	compile with debug information
#  -Wall     	give verbose compiler warnings
#  -Og       	apply moderate optimization, retain structure for debugging
#  -std=c99  	use the C99 standard language definition
#  -ffreestanding	do not assume presence of standard library (non-hosted)
CFLAGS = -g -Wall -Og -std=c99 -ffreestanding

# Configure linker options via LDFLAGS, LDLIBS
#  -nostdlib   	do not link standard system startup nor default libraries
#  -T memmap    use our linker map
LDFLAGS = -nostdlib -T memmap
LDLIBS = 

# The first target listed in the makefile is the default product built when
# make is invoked with no argument. By default, this makefile builds one
# executable and dumps the generated assembly into a text file for review
# We build .list before .bin so that we get the .list even if the link fails.
all : codegen.list codegen.bin 

# This pattern rule creates binary 'name.bin' by extracting raw instructions 
# from executable 'name.elf'
%.bin: %.elf
	arm-none-eabi-objcopy $< -O binary $@

# This pattern rule creates elf executable 'name.elf' by linking 'name.o' 
# and custom start.o (used in place of default start files). Invoke gcc
# to drive the link rather than raw ld.
%.elf: %.o start.o cstart.o
	arm-none-eabi-gcc $(LDFLAGS) $^ $(LDLIBS) -o $@

# These 2 pattern rules not truly necessary, just make explicit what was implicit...
%.o: %.c
	arm-none-eabi-gcc $(CFLAGS) -c $< -o $@
%.o: %.s
	arm-none-eabi-as $< -o $@

# This pattern rule produces 'name.list' assembly listing by disassembling 'name.o'
# This is an optional product you can make to review the assembly
%.list: %.o
	arm-none-eabi-objdump -d $< > $@

# The clean target removes previous build products, will force build a-new on next make
clean:
	rm -f *.o *.bin *.elf *.list

# Targets that aren't filename of build product should be declared "phony" 
.PHONY: all clean

# Mark these intermediate files to be saved, rather than discarded after final build
.PRECIOUS: %.o %.elf
