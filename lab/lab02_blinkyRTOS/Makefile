
PART=TM4C123GH6PM

TARGET=blinkyRtos

LABROOT=../..
TIVA=${LABROOT}/TivaDriver
FREERTOS=${LABROOT}/FreeRTOSv10.0.0/FreeRTOS/Source

SIZE=arm-none-eabi-size

ifeq ($(OS),Windows_NT)
    #Windows stuff
	FLASHOPTS=--verify --reset
	FLASH=lmflash
    CP=cp
else
    #Linux stuff
	FLASHOPTS=-v
	FLASH=lm4flash
	CP=cp
endif

#
# Echo full command lines.. Comment out for terse
VERBOSE=1
DEBUG=1

#
# Include the common make definitions.
#
include ${LABROOT}/lab/makedefs

CFLAGS += -Wno-unused-value
#CFLAGS += -save-temps

LDFLAGS += --stats

#
# Where to find source files that do not live in this directory.
#
VPATH+=${FREERTOS}
VPATH+=${FREERTOS}/portable/GCC/ARM_CM4F
VPATH+=${FREERTOS}/portable/MemMang

#
# Where to find header files that do not live in the source directory.
#
IPATH=.
IPATH+=${FREERTOS}/portable/GCC/ARM_CM4F
IPATH+=${FREERTOS}/include
IPATH+=${TIVA}

#
# The default rule, which causes the FreeRTOS example to be built.
#
all: ${COMPILER}
all: ${COMPILER}/${TARGET}.axf

#
# obscure rule to get a copy of the driver library
#
${COMPILER}/libdriver.a:
	${CP} ${TIVA}/driverlib/${COMPILER}/libdriver.a ${COMPILER}

#
# The rule to clean out all the build products.
#
clean:
	@rm -rf ${COMPILER} ${wildcard *~}

#
# The rule to create the target directory.
#
${COMPILER}:
	@mkdir -p ${COMPILER}

#
# Rules for building the FreeRTOS core.
#
#${COMPILER}/heap_2.o: ${FREERTOS}/portable/MemMang/heap_2.c

${COMPILER}/${TARGET}.axf: ${COMPILER}/heap_2.o
${COMPILER}/${TARGET}.axf: ${COMPILER}/port.o
${COMPILER}/${TARGET}.axf: ${COMPILER}/list.o
${COMPILER}/${TARGET}.axf: ${COMPILER}/queue.o
${COMPILER}/${TARGET}.axf: ${COMPILER}/tasks.o
${COMPILER}/${TARGET}.axf: ${COMPILER}/timers.o


#
# Rules for building the application
#
${COMPILER}/${TARGET}.axf: ${COMPILER}/${TARGET}.o
${COMPILER}/${TARGET}.axf: ${COMPILER}/assert.o
${COMPILER}/${TARGET}.axf: ${COMPILER}/startup_${COMPILER}.o

#
# Tiva driver library
#
${COMPILER}/${TARGET}.axf: ${COMPILER}/libdriver.a


#
# load map and device memory description
#
${COMPILER}/${TARGET}.axf: ti_tm4c123g.ld

SCATTERgcc_${TARGET}=ti_tm4c123g.ld
ENTRY_${TARGET}=ResetISR
CFLAGSgcc=-DTARGET_IS_BLIZZARD_RB1

#
# Include the automatically generated dependency files.
#
ifneq (${MAKECMDGOALS},clean)
-include ${wildcard ${COMPILER}/*.d} __dummy__
endif

#
# A rule to flash and restarrt the program
#
flash: ${COMPILER}/${TARGET}.axf
	${SIZE} ${COMPILER}/${TARGET}.axf
	${FLASH} ${FLASHOPTS} ${COMPILER}/${TARGET}.bin
