###############################################################################
## Copyright (C) 2014-2015
## file:    Makefile
## author:  gozfree <gozfree@163.com>
## created: 2015-11-10 16:29:41
## updated: 2015-11-10 16:29:41
###############################################################################

#ARCH: linux/pi/android/ios/
ARCH		?= linux
CROSS_PREFIX	?=
OUTPUT		?= /usr/local
BUILD_DIR	:= $(shell pwd)/../build/
ARCH_INC	:= $(BUILD_DIR)/$(ARCH).inc
COLOR_INC	:= $(BUILD_DIR)/color.inc

ifeq ($(ARCH_INC), $(wildcard $(ARCH_INC)))
include $(ARCH_INC)
endif

CC	= $(CROSS_PREFIX)gcc
CXX	= $(CROSS_PREFIX)g++
LD	= $(CROSS_PREFIX)ld
AR	= $(CROSS_PREFIX)ar

ifeq ($(COLOR_INC), $(wildcard $(COLOR_INC)))
include $(COLOR_INC)
else
CC_V	= $(CC)
CXX_V	= $(CXX)
LD_V	= $(LD)
AR_V	= $(AR)
CP_V	= $(CP)
RM_V	= $(RM)
endif

########
LIBNAME		= libipc
TGT_LIB_H	= $(LIBNAME).h
TGT_LIB_A	= $(LIBNAME).a
TGT_LIB_SO	= $(LIBNAME).so
TGT_UNIT_TEST	= test_$(LIBNAME)
KERNEL_MODULE	= ipc_netlink.so
KERNEL_DIR	= /lib/modules/$(shell uname -r)/build

OBJS_LIB	= $(LIBNAME).o
OBJS_LIB	+= msgq.o netlink.o shm.o
OBJS_UNIT_TEST	= test_$(LIBNAME).o

EXTRA_CFLAGS	:= -g -Wall -Werror
# -fPIC
EXTRA_CFLAGS	+= $($(ARCH)_CFLAGS)
EXTRA_CFLAGS	+= -I$(OUTPUT)/include

SHARED	:= -shared

EXTRA_LDFLAGS	:= $($(ARCH)_LDFLAGS)
EXTRA_LDFLAGS	+= -pthread

.PHONY : all clean

TGT	:= $(TGT_LIB_A)
TGT	+= $(TGT_LIB_SO)
TGT	+= $(TGT_UNIT_TEST)
TGT	+= $(KERNEL_MODULE)

OBJS	:= $(OBJS_LIB) $(OBJS_UNIT_TEST)

all: $(TGT)

%.o:%.c
	$(CC_V) -c $(EXTRA_CFLAGS) $< -o $@

$(TGT_LIB_A): $(OBJS_LIB)
	$(AR_V) rcs $@ $^

$(TGT_LIB_SO): $(OBJS_LIB)
	$(LD_V) -o $@ $^ $(SHARED)

$(TGT_UNIT_TEST): $(OBJS_UNIT_TEST) $(ANDROID_MAIN_OBJ)
	$(CC_V) -o $@ $^ $(TGT_LIB_A) $(EXTRA_LDFLAGS)

obj-m	:= netlink_kernel.o
$(KERNEL_MODULE):
	$(MAKE) -C $(KERNEL_DIR) M=$(shell pwd) modules

clean:
	$(RM_V) -f $(OBJS)
	$(RM_V) -f $(TGT)

install:
	$(MAKEDIR_OUTPUT)
	$(CP_V) -r $(TGT_LIB_H)  $(OUTPUT)/include
	$(CP_V) -r $(TGT_LIB_A)  $(OUTPUT)/lib
	$(CP_V) -r $(TGT_LIB_SO) $(OUTPUT)/lib

uninstall:
	$(RM_V) -f $(OUTPUT)/include/$(TGT_LIB_H)
	$(RM_V) -f $(OUTPUT)/lib/$(TGT_LIB_A)
	$(RM_V) -f $(OUTPUT)/lib/$(TGT_LIB_SO)