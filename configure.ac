#                                               -*- Autoconf -*-
# Process this file with autoconf to produce a configure script.

AC_PREREQ([2.68])
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
m4_include([m4/pkg.m4])

AM_MAINTAINER_MODE([enable])

# Checks for programs.
AC_PROG_CC
#AC_PROG_INSTALL        # do we need to check??

# Checks for libraries
# Checks for libusb
AC_MSG_NOTICE([Checking libusb settings.])
dnl AC_MSG_ERROR( msg, [exit status] )
dnl AC_MSG_FAILURE( msg, exit status )
dnl AC_MSG_WARN( msg )

AC_ARG_WITH(libusb_walker,
            [select libusb0 (windows default) or libusb1 (default for other systems)],
            [AC_MSG_NOTICE([libusb_walker has been called])],
            [AC_MSG_NOTICE([libusb_walker use default])]
            )

dnl -- from sane-backends configuration
dnl Enable libusb-1.0, if available
disable_libusb_1_0=no
AC_ARG_ENABLE(libusb_1_0,
    AC_HELP_STRING([--disable-libusb_1_0], [disable libusb-1.0 support even if available]),
    [ if test "x$enableval" = "xno"; then disable_libusb_1_0=yes; fi ], [])

dnl If libusb-1.0 is enabled and available, prefer that to the old libusb
dnl use? AS_VERSION_COMPARE
have_libusb_1_0=no
if test "$disable_libusb_1_0" = "no"; then
  ifdef([PKG_CHECK_MODULES],
        [PKG_CHECK_MODULES(LIBUSB_1_0, [ libusb-1.0 >= 1.0.0 ], have_libusb_1_0=yes, have_libusb_1_0=no)],
        [have_libusb_1_0=yes
  LIBUSB_1_0_CFLAGS='-I${includedir}/libusb-1.0 -I${oldincludedir}/libusb-1.0'
  LIBUSB_1_0_LIBS=-lusb-1.0])
  if test "$have_libusb_1_0" = "yes"; then
    AS_ECHO("using libusb_1.0");
    CFLAGS="$CFLAGS $LIBUSB_1_0_CFLAGS"
    LIBS="$LIBS $LIBUSB_1_0_LIBS"
    HAVE_USB=yes
  fi
fi

if test "$have_libusb_1_0" = "no"; then
    dnl Fallback to the old libusb
    dnl libusb >= 0.1.8 is required, as we need usb_interrupt_read()
    AS_ECHO("using libusb");
    AC_CHECK_HEADER(usb.h,
                    AC_CHECK_LIB(usb, usb_interrupt_read, [LIBS="$LIBS -lusb" HAVE_USB=yes])
                    )
fi

dnl The following logic is useful for distributions.  If they force
dnl USB support with --enable-libusb=yes then configure will fail
dnl when its detected that it can not be supported.  Default is
dnl "auto" which means only enable if libraries are found and do
dnl not error out.
if test "$HAVE_USB" = "no"; then
  AC_MSG_ERROR([USB support requested but required libraries not found.])
fi
if test "$HAVE_USB" = "yes"; then
  if test "$have_libusb_1_0" = "yes"; then
    AC_DEFINE(HAVE_LIBUSB_1_0, 1, [Define to 1 if you have libusb-1.0.])
  else
    AC_DEFINE(HAVE_LIBUSB, 1, [Define to 1 if you have libusb.])
  fi
fi


dnl This is the new added argument, not sure how it works yet
dnl what about AC_ARG_VAR( variable, description )
AC_ARG_ENABLE(old_libusb,
              AS_HELP_STRING([--enable-old-libusb], [use old libusb]),
              [enable_old_libusb=$enableval]
              [AC_MSG_NOTICE([old usb arg provieded])],
              [enable_old_libusb=no],
              [AC_MSG_NOTICE([no old usb arg provided])]
              )

AS_IF([test x$enable_old_libusb = xyes], 
[ dnl libusb >= 0.1.8 is required, as we need usb_interrupt_read()
    PKG_CHECK_MODULES([LIBUSB], [libusb >= 0.1.8])
    AC_DEFINE(HAVE_LIBUSB, 1, [Define to 1 if you have libusb.])
    AC_SUBST(LIBUSB_CFLAGS)
    AC_SUBST(LIBUSB_LIBS)
], [ dnl If libusb-1.0 is enabled and available, prefer that to the old libusb
    PKG_CHECK_MODULES([LIBUSB1], [libusb-1.0 >= 1.0.0])
    AC_DEFINE(HAVE_LIBUSB_1_0, 1, [Define to 1 if you have libusb-1.0.])
    AC_SUBST(LIBUSB1_CFLAGS)
    AC_SUBST(LIBUSB1_LIBS)
])

# Checks for header files.
AC_HEADER_STDC
AC_CHECK_HEADERS([stddef.h stdint.h stdlib.h string.h unistd.h])

# Checks for typedefs, structures, and compiler characteristics.
AC_C_CONST
AC_TYPE_SIZE_T
AC_HEADER_STDBOOL
AC_C_INLINE
AC_TYPE_INT16_T
AC_TYPE_INT32_T
AC_TYPE_INT8_T
AC_TYPE_UINT16_T
AC_TYPE_UINT32_T
AC_TYPE_UINT8_T

# Checks for library functions.
AC_FUNC_MALLOC
AC_FUNC_MEMCMP
#AC_CHECK_FUNCS([memset strcasecmp strncasecmp]) # action if found, action if not found
AC_CHECK_FUNCS([strcasecmp strncasecmp])
AC_CHECK_FUNC([memset], :, [AC_CHECK_LIB([libc], [libc])])

# Checks for system services.
dnl --- None for now ---

AC_CONFIG_FILES([Makefile
                 docs/Makefile
                 src/Makefile])
AC_CONFIG_FILES([fedora/dfu-programmer.spec])
AC_OUTPUT
