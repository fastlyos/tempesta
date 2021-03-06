# Copyright (C) 2020 Tempesta Technologies, Inc.
#
# This program is free software; you can redistribute it and/or modify it
# under the terms of the GNU General Public License as published by
# the Free Software Foundation; either version 2 of the License,
# or (at your option) any later version.
#
# This program is distributed in the hope that it will be useful, but WITHOUT
# ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS
# FOR A PARTICULAR PURPOSE.
# See the GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License along with
# this program; if not, write to the Free Software Foundation, Inc., 59
# Temple Place - Suite 330, Boston, MA 02111-1307, USA.

ifndef CC
	CC	= gcc
endif

CACHELINE := $(shell getconf LEVEL1_DCACHE_LINESIZE)

CFLAGS		= -O0 -ggdb -std=gnu89 \
		  -DL1_CACHE_BYTES=$(CACHELINE) -DNR_CPUS=1 \
		  -Wall -Werror -Wno-sign-compare -Wno-pointer-sign \
		  -Werror=strict-prototypes -Werror=incompatible-pointer-types \
		  -Wno-unused-but-set-variable -Wno-unused-const-variable \
		  -Wno-unused-function -Warray-bounds=0 \
		  -fno-strict-aliasing \
		  -I../../ktest -I../.. -I..
ifdef DEBUG
	CFLAGS += -DDEBUG=$(DEBUG) -DDBG_TLS=1
endif

PROC = $(shell cat /proc/cpuinfo)
ifneq (, $(findstring bmi2, $(PROC)))
	CFLAGS += -DBMI2=1
endif
ifneq (, $(findstring adx, $(PROC)))
	CFLAGS += -DADX=1
endif

TARGETS		= $(subst .c,, $(wildcard *.c))
ASM-OBJ		= bignum_x86-64.o

all : $(TARGETS) test_mpi_asm_opt
.PHONY: test_mpi_asm_opt

$(TARGETS): % : $(ASM-OBJ) %.o
	$(CC) $(CFLAGS) -o $@ $^

$(ASM-OBJ): ../$(subst .o,.S,$(ASM-OBJ))
	$(CC) $(CFLAGS) -c $^ -o $@

%.o : %.c
	$(CC) $(CFLAGS) -c $< -o $@

clean : FORCE
	rm -f *.o *~ *.orig $(TARGETS)

FORCE :
