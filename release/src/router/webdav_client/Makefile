#***********************************************************************
#
# Makefile
#
#***********************************************************************

#C = $(CC)
#TOP = $(TOP)
CFLAGS += -I$(TOP)/neon/src -I$(TOP)/libxml2/include -I$(TOP)/openssl/include
CFLAGS += -I$(SRCBASE) -I$(SRCBASE)/include -I. -I$(TOP)/shared -D_FILE_OFFSET_BITS=64 -DNVRAM_
LDFLAGS = -L$(TOP)/neon/src/.libs -L$(TOP)/libxml2/.libs -L$(TOP)/nvram${BCMEX}${EX7} -L$(TOP)/shared -L$(TOP)/zlib -L$(TOP)/openssl

# TBD...theose conflict vars should be treated as extern or static
ifeq ($(HND_ROUTER),y)
CFLAGS := $(shell echo $(CFLAGS)| sed 's/-fno-common//g')
LDFLAGS += -L$(TOP)/wlcsm -lwlcsm
endif
LIBS = -lpthread -lnvram -lneon -lxml2 -lshared -lz -lcrypto -lssl

include ../common.mak
ifeq ($(RTCONFIG_QTN),y)
LDFLAGS += -L$(TOP)/libqcsapi_client
LIBS += -lqcsapi_client
endif

ifeq ($(RTCONFIG_AMAS),y)
LDFLAGS += -L$(TOP)/json-c/.libs 
LIBS += -ljson-c
endif


ifeq ($(wildcard $(TOP)/webdav_client/*.c),)
all:
	-cp -f $(TOP)/webdav_client/prebuild/webdav_client webdav_client
else
all: webdav_client
endif

webdav_client: webdav.o base.o list.o
	$(CC) $(CFLAGS) $(LDFLAGS) $(LIBS)  $^ -o $@

.PHONY: clean
clean:
	-rm -rf *.o webdav_client

