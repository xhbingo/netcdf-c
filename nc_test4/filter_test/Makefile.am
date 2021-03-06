# Copyright 2009, UCAR/Unidata and OPeNDAP, Inc.
# See the COPYRIGHT file for more information.

# Put together AM_CPPFLAGS and AM_LDFLAGS.
include $(top_srcdir)/lib_flags.am

FILTERNAME=bzip2

BZIP2HDRS=bzlib.h bzlib_private.h
BZIP2SRC=\
      blocksort.c  \
      huffman.c    \
      crctable.c   \
      randtable.c  \
      compress.c   \
      decompress.c \
      bzlib.c

PLUGINSRC=H5Zbzip2.c
PLUGINHDRS=h5bzip2.h

#bin_PROGRAMS = fake
#fake_SOURCES = fake.c H5Zbzip2.c ${BZIP2SRC}

# Link to our assembled library.
test_filter_LDFLAGS = ${top_builddir}/liblib/libnetcdf.la
test_filter_SOURCE = test_filter.c
test_misc_LDFLAGS = ${top_builddir}/liblib/libnetcdf.la
test_misc_SOURCE = test_misc.c

BZIP2OBJ=${BZIP2SRC:%.o=%.c}

if ISCYGWIN
LIBNAME=cyg${FILTERNAME}.dll
TESTLIB=cygtest.dll
else
LIBNAME=lib${FILTERNAME}.so
TESTLIB=libtest.so
endif

EXTRA_DIST=${PLUGINSRC} ${BZIP2SRC} ${PLUGINHDRS} ${BZIP2HDRS} \
	tst_filter.sh bzip2.cdl unfiltered.cdl filtered.cdl ref_bzip2.c \
	H5Ztemplate.c h5test.h H5Ztest.c \
	CMakeLists.txt
CLEANFILES = ${LIBNAME} ${TESTLIB} *.nc *.dump tmp

if ENABLE_FILTER_TEST

check_PROGRAMS = test_filter test_misc
TESTS = tst_filter.sh

# In order to create the bzip shared object,
# we provide an explicit target for it that
# must be invoked before 'make check'

# You may need to change this if HDF5 is installed elsewhere
HDF5LIBDIR = /usr/local/lib
# Ditto for zlib
ZLIBDIR = ${HDF5LIBDIR}

BUILT_SOURCES = ${LIBNAME} ${TESTLIB}
${LIBNAME}: ${PLUGINSRC:${srcdir}%.c=%.c} ${BZIP2SRC:${srcdir}%.c=%.c}
	SDIR=${srcdir} ;\
	for f in ${PLUGINSRC} ${BZIP2SRC} ; do \
        DLLSRC="$${DLLSRC} $${SDIR}/$$f" ; \
	done ; \
	gcc -g -O0 -shared -o ${LIBNAME} $${DLLSRC} \
	-L${HDF5LIBDIR} -lhdf5_hl -lhdf5 -L${ZLIBDIR} -lz

${TESTLIB}: ${srcdir}/H5Ztest.c
	SDIR=${srcdir} ;\
        DLLSRC="$${DLLSRC} $${SDIR}/H5Ztest.c" ; \
	gcc -g -O0 -shared -o ${TESTLIB} $${DLLSRC} \
	-L${HDF5LIBDIR} -lhdf5_hl -lhdf5 -L${ZLIBDIR} -lz


endif
