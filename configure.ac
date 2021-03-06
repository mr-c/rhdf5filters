dnl Process this file with autoconf to produce a configure script.
dnl author: Mike Smith (grimbough@gmail.com)

AC_PREREQ([2.65])
AC_INIT([hdf5Filters], [0.99.3], [grimbough@gmail.com])
AC_CONFIG_SRCDIR([src])

m4_include([tools/ax_gcc_x86_cpu_supports.m4])
m4_include([tools/ax_check_compile_flag.m4])

: ${R_HOME=`R RHOME`}
if test -z "${R_HOME}"; then
  echo "could not determine R_HOME"
  exit 1
fi

AC_SUBST(CC, `"${R_HOME}/bin/R" CMD config CC`)
AC_SUBST(CFLAGS, `"${R_HOME}/bin/R" CMD config CFLAGS`)
AC_SUBST(CPICFLAGS, `"${R_HOME}/bin/R" CMD config CPICFLAGS`)
AC_SUBST(CXX, `"${R_HOME}/bin/R" CMD config CXX`)
AC_SUBST(CXXFLAGS, `"${R_HOME}/bin/R" CMD config CXXFLAGS`)
AC_SUBST(CXXPICFLAGS, `"${R_HOME}/bin/R" CMD config CXXPICFLAGS`)
AC_SUBST(MAKE, `"${R_HOME}/bin/R" CMD config MAKE`)
AC_SUBST(RANLIB, `"${R_HOME}/bin/R" CMD config RANLIB`)
AC_SUBST(LDFLAGS, `"${R_HOME}/bin/R" CMD config LDFLAGS`)

## seems unreliable as to whether this information is available
AR=`"${R_HOME}/bin/R" CMD config AR`
AS_IF([test "x$AR" = "xERROR: no information for variable 'AR'"], [AC_PATH_PROG([AR], [ar])], [])
AC_SUBST(AR)

## determine the location of the Rhdf5lib versions of the HDF5 library
RHDF5_INCLUDE=`"${R_HOME}/bin${R_ARCH_BIN}/Rscript" -e 'cat(system.file("include", package="Rhdf5lib"))'`
AC_SUBST(RHDF5_INCLUDE)

echo "configuring the BLOSC filter..."

## see if -msse2 is a supported flag
AX_CHECK_COMPILE_FLAG([-msse2], [MSSE2=true], [MSSE2=false])
## check CPU supports sse2
AX_GCC_X86_CPU_SUPPORTS(sse2, [SSE2_SUPPORTED=true], [SSE2_SUPPORTED=false])

if [ "$MSSE2" = true ] && [ "$SSE2_SUPPORTED" = true ] ; then
  SSE2="-DSHUFFLE_SSE2_ENABLED"
  MSSE2="-msse2"
  SSE2_BITSHUFFLE='bitshuffle-sse2.o: bitshuffle-sse2.c
	$(CC) $(FLAGS) -c bitshuffle-sse2.c'
  SSE2_BITSHUFFLE_OBJ='bitshuffle-sse2.o'
  SSE2_SHUFFLE='shuffle-sse2.o: shuffle-sse2.c
	$(CC) $(FLAGS) -c shuffle-sse2.c'
  SSE2_SHUFFLE_OBJ='shuffle-sse2.o'
  SSE2_OBJ_PATH='lib/blosc-1.16.3/'
else
  SSE2=""
  MSSE2=""
  SSE2_BITSHUFFLE=""
  SSE2_SHUFFLE=""
  SSE2_BITSHUFFLE_OBJ=""
  SSE2_SHUFFLE_OBJ=""
  SSE2_OBJ_PATH=""
fi

AC_SUBST(SSE2)
AC_SUBST(MSSE2)
AC_SUBST(SSE2_BITSHUFFLE)
AC_SUBST(SSE2_SHUFFLE)
AC_SUBST(SSE2_BITSHUFFLE_OBJ)
AC_SUBST(SSE2_SHUFFLE_OBJ)
AC_SUBST(SSE2_OBJ_PATH)

## see if -mavx2 is a supported flag
AX_CHECK_COMPILE_FLAG([-mavx2], [MAVX2=true], [MAVX2=false])
## check CPU supports avx2
AX_GCC_X86_CPU_SUPPORTS(avx2, [AVX2_SUPPORTED=true], [AVX2_SUPPORTED=false])

if [ "$MAVX2" = true ] && [ "$AVX2_SUPPORTED" = true ] ; then
  AVX2="-DSHUFFLE_AVX2_ENABLED"
  MAVX2="-mavx2"
  AVX2_BITSHUFFLE='bitshuffle-avx2.o: bitshuffle-avx2.c
	$(CC) $(FLAGS) -c bitshuffle-avx2.c'
  AVX2_BITSHUFFLE_OBJ='bitshuffle-avx2.o'
  AVX2_SHUFFLE='shuffle-avx2.o: shuffle-avx2.c
	$(CC) $(FLAGS) -c shuffle-avx2.c'
  AVX2_SHUFFLE_OBJ='shuffle-avx2.o'
  AVX2_OBJ_PATH='lib/blosc-1.16.3/'
else
  AVX2=""
  MAVX2=""
  AVX2_BITSHUFFLE=""
  AVX2_SHUFFLE=""
  AVX2_BITSHUFFLE_OBJ=""
  AVX2_SHUFFLE_OBJ=""
  AVX2_OBJ_PATH=""
fi

AC_SUBST(AVX2)
AC_SUBST(MAVX2)
AC_SUBST(AVX2_BITSHUFFLE)
AC_SUBST(AVX2_SHUFFLE)
AC_SUBST(AVX2_BITSHUFFLE_OBJ)
AC_SUBST(AVX2_SHUFFLE_OBJ)
AC_SUBST(AVX2_OBJ_PATH)

AC_CONFIG_FILES([src/blosc/lib/blosc-1.16.3/Makefile src/blosc/Makefile])
AC_OUTPUT

dnl update variables in Makevars
HDF5_INCLUDE="${BASEPBNAME}/src"
AC_SUBST(HDF5_INCLUDE)
AC_MSG_NOTICE([   HDF5_INCLUDE=${HDF5_INCLUDE}])

BZIP2_LIB="bzip2/libH5Zbz2.so"
BLOSC_LIB="blosc/libH5Zblosc.so"

AC_SUBST(BZIP2_LIB)
AC_SUBST(BLOSC_LIB)
AC_MSG_NOTICE([   BZIP2_LIB=${BZIP2_LIB}])
AC_MSG_NOTICE([   BLOSC_LIB=${BLOSC_LIB}])
AC_CONFIG_FILES([src/Makevars])
AC_OUTPUT
