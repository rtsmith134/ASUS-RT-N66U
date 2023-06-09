#
# Makefile for Broadcom BCM947XX boards
#
# Copyright (C) 2010, Broadcom Corporation. All Rights Reserved.
# 
# Permission to use, copy, modify, and/or distribute this software for any
# purpose with or without fee is hereby granted, provided that the above
# copyright notice and this permission notice appear in all copies.
# 
# THE SOFTWARE IS PROVIDED "AS IS" AND THE AUTHOR DISCLAIMS ALL WARRANTIES
# WITH REGARD TO THIS SOFTWARE INCLUDING ALL IMPLIED WARRANTIES OF
# MERCHANTABILITY AND FITNESS. IN NO EVENT SHALL THE AUTHOR BE LIABLE FOR ANY
# SPECIAL, DIRECT, INDIRECT, OR CONSEQUENTIAL DAMAGES OR ANY DAMAGES
# WHATSOEVER RESULTING FROM LOSS OF USE, DATA OR PROFITS, WHETHER IN AN ACTION
# OF CONTRACT, NEGLIGENCE OR OTHER TORTIOUS ACTION, ARISING OUT OF OR IN
# CONNECTION WITH THE USE OR PERFORMANCE OF THIS SOFTWARE.
#
# $Id: Makefile 258409 2011-05-09 10:57:36Z simonk $
#

# Max sizes for vanilla external
CFEZ_MAXSIZE = 131072
CFE_MAXSIZE = 262144

# Max sizes for GigE/GMAC external
ifeq ($(strip $(CFG_BCM57XX)),1)
CFEZ_MAXSIZE = 262144
CFE_MAXSIZE = 262144
endif

ifeq ($(strip $(CFG_GMAC)),1)
CFEZ_MAXSIZE = 262144
CFE_MAXSIZE = 262144
endif

# Max sizes for internal

BSPOBJS += bcm947xx_init.o bcm947xx_devs.o ui_bcm947xx.o

ifeq ($(strip $(CFG_MINIMAL_SIZE)), 1)
ifeq ($(strip $(CFG_GMAC)), 0)
ALLOBJS		+= sbsdram.o sbutils.o
CFLAGS		+= -DBCMCHIPTYPE=0
else
ALLOBJS		+= aisdram.o aiutils.o
CFLAGS		+= -DBCMCHIPTYPE=1
endif
else
ALLOBJS		+= aisdram.o aiutils.o sbsdram.o sbutils.o
endif

ALLOBJS += sisdram.o siutils.o \
	hndmips.o hndpmu.o hndchipc.o sromstubs.o bcmutils.o cfe_osl.o \
	nvram.o nvram_rw.o bcmstdlib.o

CFLAGS += -DBCM4710 -DBCM4704 -DBCMDRIVER
ifeq ($(CFG_BCMHND74K), 1)
CFLAGS += -DBCMHND74K
endif

CFLAGS += -I$(SRCBASE)/include
CFLAGS += -I$(SRCBASE)/tools/misc/lzma_src/C

vpath %.c $(SRCBASE)/shared $(SRCBASE)/shared/nvram
vpath %.S $(SRCBASE)/shared

ifeq ($(strip $(CFG_LITTLE)),0)
CFLAGS += -DIL_BIGENDIAN -D_MIPSEB_DATA_INVARIANT_
endif

ifeq ($(strip $(CFG_EMBEDDED_NVRAM)),1)
CFLAGS += -DCFG_EMBEDDED_NVRAM=1
endif

ifeq ($(strip ${CFG_MINIMAL_SIZE}),1)
CFLAGS += -DCFG_MINIMAL_SIZE=1
else
endif

ifeq ($(strip $(CFG_FLASH)),1)
CFLAGS += -DCFG_FLASH=1
CFLAGS += -D_FLASH_BROKEN_BYTEREAD_ -DINCLUDE_FLASH_DRIVERS
ALLOBJS += ui_test_flash.o
endif

ifeq ($(strip ${CFG_SFLASH}),1)
CFLAGS += -DCFG_SFLASH=1
ALLOBJS += dev_sflash.o sflash.o
endif

ifeq ($(strip ${CFG_NFLASH}),1)
CFLAGS += -DCFG_NFLASH=1 -DNFLASH_SUPPORT -DCFG_HEAP_SIZE=1024
ALLOBJS += dev_nflash.o nflash.o
endif

ifeq ($(strip $(CFG_ET)),1)

ETOBJS := etc47xx.o etc.o et_cfe.o bcmrobo.o
CFLAGS += -DCFG_ET=1 -I$(SRCBASE)/et/sys -DETROBO -DDMA

ifeq ($(strip $(CFG_GMAC)),1)
ETOBJS += etcgmac.o
CFLAGS += -DCFG_GMAC=1 -DBCMDMA64
endif

ifneq ($(strip $(CFG_MINIMAL_SIZE)),1)
ETOBJS += etc_adm.o
CFLAGS += -DETADM
endif

HNDDMA := 1

# Search for sources under src/et/sys or objects under src/et/cfe
ifneq ($(wildcard $(SRCBASE)/et/sys),)
vpath %.c $(SRCBASE)/et/sys
ALLOBJS += $(ETOBJS)
else
ALLOBJS += $(foreach obj,$(ETOBJS),$(SRCBASE)/et/cfe/$(obj))
endif
endif

ifeq ($(strip $(CFG_WL)),1)
CFLAGS += -DCFG_WL=1 -DBCMDMA64

# get wl driver source files and flags, 
# macros are defined in build/broadcom/bcm947xx/Makefile
# WLCFE is to pick wl_cfe.c from wl.mk
WLCFE=1
include $(WLCFGDIR)/$(WLCONFFILE)
include $(WLCFGDIR)/wl.mk

ifneq ($(WLFILES),)
WLOBJS := $(patsubst %.c,%.o,$(WLFILES))
else
$(error WLFILES is undefined in $(CONFIG_WL_CONF))
endif
WL_DFLAGS       := $(WLFLAGS)

ifeq ($(strip $(CFG_WLU)),1)
WLOBJS += wlu.o wlu_cmd.o wlu_iov.o bcmwifi.o
CFLAGS += -DCFG_WLU=1
endif

# add these path for WLOBJS only
$(WLOBJS): WL_CFLAGS := -I$(SRCBASE)/wl/sys -I$(SRCBASE)/wl/exe $(WL_DFLAGS)
CFLAGS += $(WL_CFLAGS) 

# Search for sources under src/wl/sys or objects under src/wl/cfe
ifneq ($(wildcard $(SRCBASE)/wl/sys/wlc.h),)
vpath %.c $(SRCBASE)/wl/sys $(SRCBASE)/wl/exe $(SRCBASE)/bcmcrypto $(SRCBASE)/shared
ALLOBJS += $(WLOBJS)
else
ALLOBJS += $(foreach obj,$(WLOBJS),$(SRCBASE)/wl/cfe/$(obj))
endif

HNDDMA := 1
endif # CFG_WL


# Search for sources under src/shared
ifeq ($(strip $(HNDDMA)),1)
ALLOBJS += hnddma.o
endif

ifeq ($(strip $(CFG_PCI)),1)
BSPOBJS += bcm947xx_pci_machdep.o hndpci.o nicpci.o 
ifeq ($(strip $(CFG_BCM57XX)),1)
CFLAGS += -DCFG_BCM57XX=1
ALLOBJS += dev_bcm5700.o hndgige.o
# Add robo if not already done by ET
ifeq ($(strip $(CFG_ET)),0)
ALLOBJS += bcmrobo.o
endif
endif
else
CFLAGS += -DBCMBUSTYPE=SI_BUS
endif
