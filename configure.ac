#                                               -*- Autoconf -*-
# Process this file with autoconf to produce a configure script.

# TODO : add libusb build info to --help

AC_PREREQ([2.59])
AC_INIT([dfu-programmer],[0.7.2],[],[],[https://github.com/dfu-programmer/dfu-programmer])

# package configuration
AC_CONFIG_SRCDIR([src/atmel.c])
AC_CONFIG_AUX_DIR([m4])
dnl AC_REQUIRE_AUX_FILE()
dnl AC_CONFIG_MACRO_DIR()

AM_INIT_AUTOMAKE()

AC_CONFIG_HEADERS([src/config.h])
dnl AC_CONFIG_COMMANDS([timestamp], [date >timestamp])
dnl AC_CONFIG_LINKS

# Enable silent rules if available
m4_ifdef([AM_SILENT_RULES], [AM_SILENT_RULES([yes])])

AM_MAINTAINER_MODE([enable])

# Checks for programs.
AC_PROG_CC
#AC_PROG_INSTALL        # do we need to check??

# Checks for libraries.
# Check for libusb
AC_MSG_NOTICE([ ====== Checking libusb settings ====== ])
dnl AC_MSG_ERROR( msg, [exit status] )
dnl AC_MSG_FAILURE( msg, exit status )
dnl AC_MSG_WARN( msg )

dnl -- from sane-backends configuration
# Use libusb-1.0, if it is available and not disabled by user
# then fall back on libusb-0.1 if not disabled by user
have_libusb_1_0=no
have_libusb_0_1=no
AC_ARG_ENABLE( libusb_1_0,
              AC_HELP_STRING([--disable-libusb_1_0], [Disable libusb-1.0 support even if available]),
              [ if test "x$enableval" != "xno"; then enable_libusb_1_0=yes; fi ],
              [enable_libusb_1_0=yes] )

AC_ARG_ENABLE( libusb_0_1,
              AC_HELP_STRING([--disable-libusb_0_1], [Do not fall back on libusb-0.1 if libusb-1.0 is not found]),
              [ if test "x$enableval" != "xno"; then enable_libusb_0_1=yes; fi ],
              [enable_libusb_0_1=yes] )

# check that at least one libusb version is enabled
AS_IF([test $enable_libusb_1_0 = no],
      [AS_IF([test $enable_libusb_0_1 = no],
             [AC_MSG_ERROR( [At least one libusb must be enabled!!])],
             [AC_MSG_NOTICE( [Forcing use of libusb-0.1])])],
      [AS_IF([test $enable_libusb_0_1 = no],
             [AC_MSG_NOTICE( [Forcing use of libusb-1.0])])])


#AC_CHECK_PROG([has_pkg_config_prog], pkg-config, [yes], [no])
#  AS_IF([test "x$ac_cv_prog_has_pkg_config_prog" = "xyes"],[dnl
#    AC_MSG_NOTICE( "Checking for libusb-1.0" )
#    AS_IF([test pkg-config\ --atleast-version=1.0.0\ libusb-1.0],[dnl
#      have_libusb_1_0=yes
#      LIBUSB1_FLAGS=`pkg-config --cflags libusb-1.0`
#      LIBUSB1_LIBS=`pkg-config --libs libusb-1.0`
#    ])
#  ])

ifdef( [PKG_CHECK_MODULES], [has_pkg_config=yes], [has_pkg_config=no] )

# check libusb 1.0
AS_IF([test "$enable_libusb_1_0" = "yes"],[dnl
  AS_IF([test "$has_pkg_config" = "yes"],[dnl
    PKG_CHECK_MODULES([LIBUSB_1_0], [libusb-1.0 >= 1.0.0],
                      [have_libusb_1_0=yes],
                      [AC_MSG_WARN("libusb-1.0 not found")])
  ],[dnl
    AC_MSG_WARN( [No pkg-config found, guessing libusb-1.0 flags / location] )
    #AC_CHECK_LIB(usb, usb_interrupt_read, have_libusb_0_1=yes))
    dnl use AS_VERSION_COMPARE?
    [have_libusb_1_0=yes]
    [LIBUSB_1_0_CFLAGS='-I${includedir}/libusb-1.0 -I${oldincludedir}/libusb-1.0']
    [LIBUSB_1_0_LIBS="-lusb-1.0"]
  ])
])

# check libusb 0.1
AS_IF([test "$have_libusb_1_0" = "no"],[dnl
  AS_IF([test "$enable_libusb_0_1" = "yes"],[dnl
    AC_MSG_NOTICE( "Checking for libusb-0.1" )
    dnl Fallback to the old libusb
    dnl libusb >= 0.1.8 is required, as we need usb_interrupt_read()
    AC_CHECK_HEADER( usb.h,
       AC_CHECK_LIB(usb, usb_interrupt_read,
            LIBUSB_0_1_CFLAGS=""
            LIBUSB_0_1_LIBS="-lusb"
            [have_libusb_0_1=yes],
            [AC_MSG_WARN("libusb-0.1 not found")]))
  ])
])

# check that some version of libusb was found
AS_IF([test "$have_libusb_1_0" = "yes"],[dnl
  AC_MSG_NOTICE("Using libusb-1.0")
  AC_DEFINE( HAVE_LIBUSB_1_0, 1, [Define to 1 if you have libusb-1.0.] )
  AC_SUBST(LIBUSB_1_0_CFLAGS)
  AC_SUBST(LIBUSB_1_0_LIBS)
],[AS_IF([test "$have_libusb_0_1" = "yes"],[
  AC_MSG_NOTICE("Using libusb-0.1")
  AC_DEFINE( HAVE_LIBUSB, 1, [Define to 1 if you have libusb-0.1.] )
  AC_SUBST(LIBUSB_0_1_CFLAGS)
  AC_SUBST(LIBUSB_0_1_LIBS)
],[
  AC_MSG_ERROR([No libusb support libraries were found.])
])])

# Checks for header files.
AC_HEADER_STDC
AC_CHECK_HEADERS([stddef.h stdint.h stdlib.h string.h unistd.h])

# Checks for typedefs, structures, and compiler characteristics.
AC_C_CONST
AC_TYPE_SIZE_T
AC_HEADER_STDBOOL
AC_C_INLINE
AC_TYPE_INT8_T
AC_TYPE_INT16_T
AC_TYPE_INT32_T
AC_TYPE_UINT8_T
AC_TYPE_UINT16_T
AC_TYPE_UINT32_T

# Checks for library functions.
AC_FUNC_MALLOC
AC_FUNC_MEMCMP
#AC_CHECK_FUNCS([memset strcasecmp strncasecmp]) # action if found, action if not found
AC_CHECK_FUNCS([strcasecmp strncasecmp])
AC_CHECK_FUNC([memset], :, [AC_CHECK_LIB([libc], [libc])])

# Checks for system services.
dnl --- None for now ---

AC_CONFIG_FILES([fedora/dfu-programmer.spec])
AC_CONFIG_FILES([Makefile
                 docs/Makefile
                 src/Makefile])
AC_OUTPUT

# vim:shiftwidth=2
