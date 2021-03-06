FIND_PACKAGE (PythonLibs)
FIND_PACKAGE (PythonInterp REQUIRED)
EXECUTE_PROCESS(COMMAND ${PYTHON_EXECUTABLE} -c "from sys import stdout; from distutils import sysconfig; stdout.write(sysconfig.get_python_lib(True))" OUTPUT_VARIABLE PYTHON_INSTALL_DIR)

MESSAGE (STATUS "Python executable: ${PYTHON_EXECUTABLE}")
MESSAGE (STATUS "Python installation dir: ${PYTHON_INSTALL_DIR}")

ADD_CUSTOM_COMMAND (
    OUTPUT solv_python.c
    COMMAND ${SWIG_EXECUTABLE} -python -I${CMAKE_SOURCE_DIR}/src -o solv_python.c ${CMAKE_SOURCE_DIR}/bindings/solv.i
    WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
    DEPENDS ${CMAKE_SOURCE_DIR}/bindings/solv.i
)
INCLUDE_DIRECTORIES (${PYTHON_INCLUDE_PATH})

ADD_LIBRARY (bindings_python SHARED solv_python.c)
SET_TARGET_PROPERTIES (bindings_python PROPERTIES PREFIX "" OUTPUT_NAME "_solv")
TARGET_LINK_LIBRARIES (bindings_python libsolvext libsolv ${SYSTEM_LIBRARIES})

INSTALL (TARGETS bindings_python LIBRARY DESTINATION ${PYTHON_INSTALL_DIR})
INSTALL (FILES ${CMAKE_CURRENT_BINARY_DIR}/solv.py DESTINATION ${PYTHON_INSTALL_DIR})

