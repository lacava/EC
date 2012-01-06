### configure.ac                                        -*- Autoconf -*-
###
### Process this file with autoconf to produce a configure script.
###
### Evaporative Cooling (EC)
### Daniel Schwarz (c) 2005-2012
### http://insilico.utulsa.edu/evaporative-cooling

AC_INIT([Evaporative Cooling], [1.0], [bill-whit@utulsa.edu], [ec], [http://insilico.utulsa.edu/evaporative-cooling])
AC_CONFIG_MACRO_DIR([m4])

AC_CONFIG_SRCDIR(src/library/EvaporativeCooling.cpp)
AC_CONFIG_AUX_DIR([build-aux])
AM_INIT_AUTOMAKE
AC_CONFIG_HEADERS([config.h])

AC_SUBST(SRCDIR, src)
AC_SUBST(LIBDIR, library)

AC_CANONICAL_HOST

# add parameter(s)
AC_ARG_ENABLE(debug, AC_HELP_STRING([--enable-debug],[Compile code for debugging.]), [debug_rjungle=yes])
AC_ARG_ENABLE(mpi, AC_HELP_STRING([--enable-mpi],[Compile code for MPI support (use mpic++ not g++ compiler).]), [with_mpi=yes])
AC_ARG_ENABLE(timeprof, AC_HELP_STRING([--enable-timeprof],[Get time profiling of internal functions.]), [with_timeprof=no])
AC_ARG_ENABLE(windows, AC_HELP_STRING([--enable-windows],[Compile code for Windows platform using cygwin]), [MAKE_FOR_WINDOWS=yes], [MAKE_FOR_WINDOWS=no])

#
# Compiler config
#
# Set variables
  #AC_PROG_CXX([g++-4.3.3 g++-4.3.1 g++-4.3.0 g++-4 g++ gcc cl KCC CC cxx cc++ xlC aCC c++])
  #AC_PROG_CC([gcc-4.3.3 gcc-4.3.1 gcc-4.3.0 gcc-4 gcc])
  #AC_PROG_F77([gfortran-4.3.3 gfortran-4 gfortran g++-4.3.1 g++-4.3.0])
  AC_PROG_CXX([g++ gcc cl KCC CC cxx cc++ xlC aCC c++])
  AC_PROG_CC([gcc])
  AC_PROG_F77([gfortran])
echo

#
# Time profiling
#
if test "$with_timeprof" == 'yes' ; then
  AC_DEFINE([HAVE_TIMEPROF], 1, [Time profiling])
fi

# Checks for programs.
AC_PROG_CPP
AC_PROG_LN_S
AC_PROG_LIBTOOL

# Checks for header files.
AC_CHECK_HEADERS([math.h stdlib.h string.h sys/time.h getopt.h])

# Checks for typedefs, structures, and compiler characteristics.
AC_HEADER_STDBOOL
AC_C_INLINE
AC_TYPE_SIZE_T

# Check pointer size
AC_CHECK_SIZEOF(void *)

# Check for openmp
AC_OPENMP

# boost.m4
BOOST_REQUIRE([1.34])
BOOST_PROGRAM_OPTIONS
BOOST_FIND_HEADER(boost/program_options.hpp, AC_MSG_ERROR([could not find required headers of Boost c++. Install boost c++ libraries.],))

# gsl.m4
AX_PATH_GSL(1.8,, AC_MSG_ERROR([could not find required version of GSL]))

# libxml2.m4
AM_PATH_XML2(2.0.0,, AC_MSG_ERROR([could not find required version of XML2]))

CXXFLAGS="-Wall $CXXFLAGS"
CXXFLAGS="$CXXFLAGS -D__NOPLUGIN__ -D_FILE_OFFSET_BITS=64 -DHAVE__BOOL -DNDEBUG"
CXXFLAGS="$CXXFLAGS -ansi -Wno-long-long"

LIBS="-lrjungle-1.2.362 -llr-1.2.362 $LIBS"

# Checks for library functions.
AC_FUNC_ERROR_AT_LINE

#
# Check for XML delegate library.
#
AC_ARG_WITH([xml],
    [AC_HELP_STRING([--without-xml],
                    [disable XML support])],
    [with_xml=$withval],
    [with_xml=$have_x])

if test "$with_xml" != 'yes' ; then
    DISTCHECK_CONFIG_FLAGS="${DISTCHECK_CONFIG_FLAGS} --with-xml=$with_xml "
fi

have_xml='no'
XML_LIBS=''
if test "$with_xml" != 'no'; then
    AC_MSG_CHECKING([for XML])
    AC_MSG_RESULT([])
    PERSIST_LDFLAGS=$LDFLAGS
    PERSIST_CPPFLAGS=$CPPFLAGS
    xml2_config=''
    AC_CHECK_PROGS(xml2_config,xml2-config,)dnl
    if test -n "$xml2_config"; then
        # Debian installs libxml headers under /usr/include/libxml2/libxml with
        # the shared library installed under /usr/lib, whereas the package
        # installs itself under $prefix/libxml and $prefix/lib.
        xml2_prefix=`xml2-config --prefix`
        if test -d "${xml2_prefix}/include/libxml2"; then
            CPPFLAGS="$CPPFLAGS -I${xml2_prefix}/include/libxml2"
        fi
        if test "${xml2_prefix}" != '/usr'; then
            LDFLAGS="$LDFLAGS -L${xml2_prefix}/lib"
        fi
    fi
    failed=0
    passed=0
    AC_CHECK_HEADER(libxml/parser.h,passed=`expr $passed + 1`,failed=`expr $failed + 1`)
    AC_CHECK_LIB(xml2,xmlParseExternalEntity,passed=`expr $passed + 1`,failed=`expr $failed + 1`,)
    AC_MSG_CHECKING([if XML package is complete])
    if test $passed -gt 0; then
        if test $failed -gt 0; then
            AC_MSG_RESULT([no -- some components failed test])
            have_xml='no (failed tests)'
            LDFLAGS="$PERSIST_LDFLAGS"
            CPPFLAGS="$PERSIST_CPPFLAGS"
        else
            XML_LIBS='-lxml2'
            LIBS="$XML_LIBS $LIBS"
            AC_DEFINE(XML_DELEGATE,1,Define if you have XML library)
            AC_MSG_RESULT([yes])
            have_xml='yes'
        fi
    else
        AC_MSG_RESULT([no])
    fi
fi

AM_CONDITIONAL(XML_DELEGATE,test "$have_xml" = 'yes')
AC_SUBST(XML_LIBS)

dnl ===========================================================================

AC_MSG_NOTICE([--------------------------------------------------])

if test -z "$OPENMP_CFLAGS" ; then
  AC_MSG_WARN([Could not detect OpenMP support.])
  AC_MSG_NOTICE([If you have got multiple CPUs in your computer the speed of the program will drastically increase with OpenMP.])
  AC_MSG_NOTICE([Install a compiler with OpenMP support for better performance.])
fi

# Process makefiles
AC_CONFIG_FILES([Makefile src/library/Makefile src/Makefile])
AC_OUTPUT

LT_INIT

######################################################################
# DOXYGEN SUPPORT
######################################################################

DX_HTML_FEATURE(ON)
DX_CHM_FEATURE(OFF)
DX_CHI_FEATURE(OFF)
DX_MAN_FEATURE(OFF)
DX_RTF_FEATURE(OFF)
DX_XML_FEATURE(OFF)
DX_PDF_FEATURE(OFF)
DX_PS_FEATURE(OFF)

DX_INIT_DOXYGEN([$PACKAGE_NAME],[doxygen.cfg])

######################################################################