
INCLUDE (CheckFunctionExists)
CHECK_FUNCTION_EXISTS (qsort_r HAVE_QSORT_R)
CHECK_FUNCTION_EXISTS (__qsort_r HAVE___QSORT_R)

IF (HAVE_QSORT_R)
  ADD_DEFINITIONS (-DHAVE_QSORT_R=1)
ENDIF (HAVE_QSORT_R)

IF (HAVE___QSORT_R)
  ADD_DEFINITIONS (-DHAVE___QSORT_R=1)
ENDIF (HAVE___QSORT_R)

ADD_DEFINITIONS (-DLIBSOLV_INTERNAL=1)

SET (libsolv_SRCS
    bitmap.c poolarch.c poolvendor.c poolid.c strpool.c dirpool.c
    solver.c solverdebug.c repo_solv.c evr.c pool.c
    queue.c repo.c repodata.c repopage.c util.c policy.c solvable.c
    transaction.c rules.c problems.c
    chksum.c md5.c sha1.c sha2.c solvversion.c)

SET (libsolv_HEADERS
    bitmap.h evr.h hash.h policy.h poolarch.h poolvendor.h pool.h
    poolid.h pooltypes.h queue.h solvable.h solver.h solverdebug.h
    repo.h repodata.h repopage.h repo_solv.h util.h
    strpool.h dirpool.h knownid.h transaction.h rules.h problems.h
    chksum.h dataiterator.h ${CMAKE_BINARY_DIR}/src/solvversion.h)

SET (CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -fPIC")
SET (CMAKE_SHARED_LINKER_FLAGS "${LINK_FLAGS} -Wl,--version-script=${CMAKE_SOURCE_DIR}/src/libsolv.ver")

IF (DISABLE_SHARED)
ADD_LIBRARY (libsolv STATIC ${libsolv_SRCS})
ELSE (DISABLE_SHARED)
ADD_LIBRARY (libsolv SHARED ${libsolv_SRCS})
ENDIF (DISABLE_SHARED)

SET_TARGET_PROPERTIES(libsolv PROPERTIES OUTPUT_NAME "solv")
SET_TARGET_PROPERTIES(libsolv PROPERTIES SOVERSION ${LIBSOLV_SOVERSION})

INSTALL (FILES ${libsolv_HEADERS} DESTINATION "${CMAKE_INSTALL_PREFIX}/include/solv")
INSTALL (TARGETS libsolv LIBRARY DESTINATION ${LIB_INSTALL_DIR} ARCHIVE DESTINATION ${LIB_INSTALL_DIR})

IF (ENABLE_STATIC AND NOT DISABLE_SHARED)
ADD_LIBRARY (libsolv_static STATIC ${libsolv_SRCS})
SET_TARGET_PROPERTIES(libsolv_static PROPERTIES OUTPUT_NAME "solv")
SET_TARGET_PROPERTIES(libsolv_static PROPERTIES SOVERSION ${LIBSOLV_SOVERSION})
INSTALL (TARGETS libsolv_static LIBRARY DESTINATION ${LIB_INSTALL_DIR} ARCHIVE DESTINATION ${LIB_INSTALL_DIR})
ENDIF (ENABLE_STATIC AND NOT DISABLE_SHARED)
