#
# Linux acsd Makefile
#
# Copyright (C) 2012, Broadcom Corporation
# All Rights Reserved.
# 
# This is UNPUBLISHED PROPRIETARY SOURCE CODE of Broadcom Corporation;
# the contents of this file may not be disclosed to third parties, copied
# or duplicated in any form, in whole or in part, without the prior
# written permission of Broadcom Corporation.
#
# $Id: Makefile 394886 2013-04-04 02:48:41Z $
#
include ../.config

CFLAGS	+= -I. -I$(TOP)/shared -I$(SRCBASE)/include -Wall -Werror
CFLAGS	+= $(if $(WLAN_ComponentIncPath),$(WLAN_ComponentIncPath),$(addprefix -I,$(wildcard $(SRCBASE)/shared/bcmwifi/include)))
#CFLAGS	+= -g -DDEBUG
#CFLAGS	+= -DACS_DEBUG
CFLAGS	+= -s -O2
LDFLAGS += -L$(TOP)/nvram -L$(INSTALLDIR)/nvram/usr/lib -lnvram
LDFLAGS += -L$(TOP)/shared -L$(INSTALLDIR)/shared/usr/lib -lshared
LDFLAGS += $(EXTRA_LDFLAGS)


vpath %.c $(SRCBASE)/shared/ $(SRCBASE)/shared/bcmwifi/src

ifeq ($(wildcard $(SRCBASE)/router/acsd/*.c),)
all:
	-cp -f prebuilt/acsd acsd
#	-cp -f prebuilt/acs_cli acs_cli
else
all: acsd #acs_cli
endif

OBJS = chan_utils.o dcs.o acs.o acsd_utils.o acsd_main.o acsd_cmd.o acsd_chanim.o bcmwifi_channels.o
OBJS += acs_dfsr.o

install: all
	install -d $(INSTALLDIR)/usr/sbin/
	install acsd $(INSTALLDIR)/usr/sbin/
#	install acs_cli $(INSTALLDIR)/usr/sbin/
	$(STRIP) $(INSTALLDIR)/usr/sbin/acsd
#	$(STRIP) $(INSTALLDIR)/usr/sbin/acs_cli

clean:
	rm -f *.o *.a *.so acsd acs_cli

${OBJS}: acsd.h acsd_svr.h acs_dfsr.h

acsd: bcmutils.o ${OBJS}
	$(CC) $(CFLAGS) $^ -o $@  $(LDFLAGS)

acs_cli: acsd_cli.o acsd_utils.o
	$(CC) $(CFLAGS) $^ -o $@  $(LDFLAGS)

