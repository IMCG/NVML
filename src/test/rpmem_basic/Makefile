#
# Copyright 2016, Intel Corporation
#
# Redistribution and use in source and binary forms, with or without
# modification, are permitted provided that the following conditions
# are met:
#
#     * Redistributions of source code must retain the above copyright
#       notice, this list of conditions and the following disclaimer.
#
#     * Redistributions in binary form must reproduce the above copyright
#       notice, this list of conditions and the following disclaimer in
#       the documentation and/or other materials provided with the
#       distribution.
#
#     * Neither the name of the copyright holder nor the names of its
#       contributors may be used to endorse or promote products derived
#       from this software without specific prior written permission.
#
# THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS
# "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT
# LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR
# A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT
# OWNER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL,
# SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT
# LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE,
# DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY
# THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT
# (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
# OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
#

#
# src/test/rpmem_basic/Makefile -- build rpmem_basic test
#
include ../../common.inc

vpath %.c ../../common
vpath %.c ../../rpmem_common

ifeq ($(BUILD_RPMEM), y)
TARGET = rpmem_basic
OBJS = rpmem_basic.o\
       rpmem_fip_common.o\
       rpmem_common.o\
       base64.o\
       util.o\
       util_linux.o\
       out.o set.o set_linux.o

LIBRPMEM=y
LIBPMEM=y
endif

include ../Makefile.inc

ifeq ($(BUILD_RPMEM), y)
out.o: CFLAGS += -DDEBUG -DSRCVERSION=\"utversion\"
LIBS += $(shell $(PKG_CONFIG) --libs libfabric)
CFLAGS += $(shell $(PKG_CONFIG) --cflags libfabric)
INCS += -I../../common
INCS += -I../../rpmem_common
endif
