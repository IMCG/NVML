#
# Copyright 2014-2016, Intel Corporation
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
# doc/Makefile -- Makefile for NVM library man pages
#

include ../src/common.inc

MANPAGES_3_MD =
MANPAGES_1_MD =

MANPAGES_3_GROFF = $(MANPAGES_3_MD:.3.md=.3)
MANPAGES_1_GROFF = $(MANPAGES_1_MD:.1.md=.1)

MANPAGES_3 = libpmem.3 libpmemblk.3 libpmemlog.3 libpmemobj.3 libpmempool.3 \
	libvmem.3 libvmmalloc.3 \
	$(MANPAGES_3_GROFF)
MANPAGES_1 = pmempool.1 pmempool-info.1 pmempool-create.1 \
	pmempool-check.1 pmempool-dump.1 pmempool-rm.1 pmempool-convert.1 \
	$(MANPAGES_1_GROFF)

ifeq ($(BUILD_RPMEM),y)
MANPAGES_3_MD_EXP = librpmem.3.md
MANPAGES_1_MD_EXP = rpmemd.1.md
endif

MANPAGES_3_GROFF_EXP = $(MANPAGES_3_MD_EXP:.3.md=.3)
MANPAGES_1_GROFF_EXP = $(MANPAGES_1_MD_EXP:.1.md=.1)

ifeq ($(EXPERIMENTAL),y)
MANPAGES_3 += $(MANPAGES_3_GROFF_EXP)
MANPAGES_1 += $(MANPAGES_1_GROFF_EXP)
else
MANPAGES_3_NOINSTALL += $(MANPAGES_3_GROFF_EXP)
MANPAGES_1_NOINSTALL += $(MANPAGES_1_GROFF_EXP)
endif

MANPAGES = $(MANPAGES_1) $(MANPAGES_3)\
	   $(MANPAGES_1_NOINSTALL) $(MANPAGES_3_NOINSTALL)

HTMLFILES = $(MANPAGES:=.html)
TXTFILES = $(MANPAGES:=.txt)

GZFILES_1 = $(MANPAGES_1:=.gz)
GZFILES_3 = $(MANPAGES_3:=.gz)
GZFILES_1_NOINSTALL = $(MANPAGES_1_NOINSTALL:=.gz)
GZFILES_3_NOINSTALL = $(MANPAGES_3_NOINSTALL:=.gz)
GZFILES = $(GZFILES_1) $(GZFILES_3)\
	  $(GZFILES_1_NOINSTALL) $(GZFILES_3_NOINSTALL)
DOXYGEN_HTMLDIR=cpp_html

MANPAGES_DESTDIR_1 = $(DESTDIR)$(man1dir)
MANPAGES_DESTDIR_3 = $(DESTDIR)$(man3dir)

DOCS_DESTDIR = $(DESTDIR)$(docdir)
CPP_DOC_DIR ?= libpmemobj++-dev
CPP_DOCS_DESTDIR = $(DOCS_DESTDIR)/$(CPP_DOC_DIR)

all: $(MANPAGES) $(TXTFILES)

%.txt: %
	man ./$< > $@

groff: $(MANPAGES_3) $(MANPAGES_1)

doxygen_docs:
	doxygen cppobj.Doxyfile

html: $(HTMLFILES) doxygen_docs

%.html: %
	groff -mandoc -Thtml ./$< > $@

LICENSE: ../LICENSE
	sed -n '1,/DAMAGE\./p' ./$< | sed 's/^/\.\\\"\ /g' | sed 's/^\.\\\"\ $$/\.\\\"/g' > $@

%.3: %.3.md LICENSE ../utils/md2man.sh
	../utils/md2man.sh ./$< LICENSE > $@

%.1: %.1.md LICENSE ../utils/md2man.sh
	../utils/md2man.sh ./$< LICENSE > $@

compress: $(GZFILES)

%.gz: %
	gzip -c ./$< > $@

clean:

clobber: clean
	$(RM) -rf $(TXTFILES) $(HTMLFILES) $(GZFILES) $(DOXYGEN_HTMLDIR) \
		$(MANPAGES_1_GROFF) $(MANPAGES_3_GROFF) \
		$(MANPAGES_1_GROFF_EXP) $(MANPAGES_3_GROFF_EXP) \
		LICENSE

ifeq ($(EXPERIMENTAL),y)
install-cpp: doxygen_docs
	$(call install_recursive,$(DOXYGEN_HTMLDIR),0644,$(CPP_DOCS_DESTDIR))

install: install-cpp

uninstall-cpp:
	$(RM) -rf $(CPP_DOCS_DESTDIR)

uninstall: uninstall-cpp
endif

install: compress
	install -d $(MANPAGES_DESTDIR_1)
	install -p -m 0644 $(GZFILES_1) $(MANPAGES_DESTDIR_1)
	install -d $(MANPAGES_DESTDIR_3)
	install -p -m 0644 $(GZFILES_3) $(MANPAGES_DESTDIR_3)

uninstall:
	$(foreach f, $(GZFILES_1), $(RM) $(MANPAGES_DESTDIR_1)/$(f))
	$(foreach f, $(GZFILES_3), $(RM) $(MANPAGES_DESTDIR_3)/$(f))

.PHONY: all html clean compress clobber cstyle install uninstall install-cpp\
	uninstall-cpp doxygen_docs
