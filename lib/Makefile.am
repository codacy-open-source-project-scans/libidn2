# Copyright (C) 2011-2024 Simon Josefsson

# This program is free software: you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation, either version 3 of the License, or
# (at your option) any later version.

# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.

# You should have received a copy of the GNU General Public License
# along with this program.  If not, see <http://www.gnu.org/licenses/>.

AM_CPPFLAGS = -DIDN2_BUILDING -DSRCDIR=\"$(srcdir)\"
AM_CPPFLAGS += -I$(top_builddir)/gl -I$(top_srcdir)/gl
AM_CPPFLAGS += -I$(top_srcdir)/unistring/ -I$(top_builddir)/unistring/

AM_CFLAGS = $(WERROR_CFLAGS) $(WARN_CFLAGS)
AM_CFLAGS += $(CFLAG_VISIBILITY) $(CODE_COVERAGE_CFLAGS)

lib_LTLIBRARIES = libidn2.la

include_HEADERS = idn2.h

libidn2_la_SOURCES = idn2.map idn2.h
libidn2_la_SOURCES += idna.h idna.c
libidn2_la_SOURCES += lookup.c decode.c
libidn2_la_SOURCES += register.c
libidn2_la_SOURCES += bidi.h bidi.c
libidn2_la_SOURCES += version.c
libidn2_la_SOURCES += error.c
libidn2_la_SOURCES += punycode.c
libidn2_la_SOURCES += free.c
libidn2_la_SOURCES += data.h data.c
libidn2_la_SOURCES += tr46map.h tr46map.c
libidn2_la_SOURCES += tables.h tables.c
libidn2_la_SOURCES += context.h context.c

libidn2_la_LIBADD = $(LTLIBICONV) $(LTLIBINTL)
libidn2_la_LIBADD += ../unistring/libunistring.la $(LTLIBUNISTRING)
libidn2_la_LIBADD += ../gl/libgnu.la

libidn2_la_LDFLAGS = -version-info $(LT_CURRENT):$(LT_REVISION):$(LT_AGE) \
	-no-undefined
libidn2_la_LDFLAGS += $(CODE_COVERAGE_LDFLAGS)

IDNA_TABLE = $(srcdir)/Idna2008-15.0.0.txt
IANA_URL = https://www.unicode.org/Public/idna/idna2008derived/Idna2008-15.0.0.txt
IDNA_TABLE_SHA1 = 6aad2582843823a6a91d188c5296c90dc8733df0

TR46MAP = $(srcdir)/IdnaMappingTable.txt
TR46MAP_URL = https://www.unicode.org/Public/idna/15.0.0/IdnaMappingTable.txt
TR46MAP_SHA1 = dc4c1f42fc53b3f618b153dd03a8a1a728ccd13f

NFCQC = $(srcdir)/DerivedNormalizationProps.txt
NFCQC_URL = https://www.unicode.org/Public/15.0.0/ucd/DerivedNormalizationProps.txt
NFCQC_SHA1 = 7f48197587f7b5675d671a3dc52b866eb1d76eff

BUILT_SOURCES = data.c tr46map_data.c
DISTCLEANFILES = $(BUILT_SOURCES)
CLEANFILES = gendata$(EXEEXT) gentr46map$(EXEEXT)
MAINTAINERCLEANFILES = $(IDNA_TABLE) $(TR46MAP) $(NFCQC)
EXTRA_DIST = $(IDNA_TABLE) $(TR46MAP) $(NFCQC) data.c gendata.c tr46map_data.c gentr46map.c

gendata$(EXEEXT): gendata.c
	$(CC) $(DEFAULT_INCLUDES) $(AM_CPPFLAGS) $(CFLAGS) $< -o $@
data.c: $(srcdir)/gendata.c $(IDNA_TABLE)
	$(MAKE) gendata$(EXEEXT)
	$(builddir)/gendata$(EXEEXT) < $(IDNA_TABLE) > $@.new
	mv $@.new $@

gentr46map$(EXEEXT): gentr46map.c
	$(CC) $(DEFAULT_INCLUDES) $(AM_CPPFLAGS) $(CFLAGS) $< -o $@
tr46map_data.c: $(srcdir)/gentr46map.c $(TR46MAP) $(NFCQC)
	$(MAKE) gentr46map$(EXEEXT)
	$(builddir)/gentr46map$(EXEEXT) > $@.new
	mv $@.new $@

$(IDNA_TABLE):
	if ! echo "$(IDNA_TABLE_SHA1) $(IDNA_TABLE)" | sha1sum -c -; then \
	  rm -f $(IDNA_TABLE); \
	  wget -O$(IDNA_TABLE) $(IANA_URL); \
	  echo "$(IDNA_TABLE_SHA1) $(IDNA_TABLE)" | sha1sum -c -; \
	fi

$(TR46MAP):
	if ! echo "$(TR46MAP_SHA1) $(TR46MAP)" | sha1sum -c -; then \
	  rm -f $(TR46MAP); \
	  wget -O$(TR46MAP) $(TR46MAP_URL); \
	  echo "$(TR46MAP_SHA1) $(TR46MAP)" | sha1sum -c -; \
	fi

$(NFCQC):
	if ! echo "$(NFCQC_SHA1) $(NFCQC)" | sha1sum -c -; then \
	  rm -f $(NFCQC); \
	  wget -O$(NFCQC) $(NFCQC_URL); \
	  echo "$(NFCQC_SHA1) $(NFCQC)" | sha1sum -c -; \
	fi

if HAVE_LD_VERSION_SCRIPT
libidn2_la_LDFLAGS += -Wl,--version-script=$(srcdir)/idn2.map
else
libidn2_la_LDFLAGS += -export-symbols-regex '^(idn2_.*|_idn2_punycode_encode|_idn2_punycode_decode)$$'
endif

if HAVE_LD_OUTPUT_DEF
libidn2_la_LDFLAGS += -Wl,--output-def,libidn2-$(DLL_VERSION).def
libidn2-$(DLL_VERSION).def: libidn2.la
defexecdir = $(libdir)
defexec_DATA = libidn2-$(DLL_VERSION).def
DISTCLEANFILES += $(defexec_DATA)
endif
