cmake_minimum_required(VERSION 3.0.0)

project(qhexedit2)

set(PROJECT_VERSION 0.7.8)

#SET(CMAKE_MODULE_PATH "${CMAKE_ROOT}/cmake/Modules")

find_package(Qt4 COMPONENTS QtCore QtGui REQUIRED)
INCLUDE(${QT_USE_FILE})
ADD_DEFINITIONS(${QT_DEFINITIONS})
set(CMAKE_AUTOMOC TRUE)
set(CMAKE_AUTOUIC TRUE)

set(CMAKE_INCLUDE_CURRENT_DIR ON)

# library
file(GLOB WIDGET_SOURCES "src/*.cpp")
file(GLOB WIDGET_HEADERS "src/*.h")
add_library(qhexedit SHARED ${WIDGET_SOURCES} ${WIDGET_HEADERS} ${QM_TRANSLATIONS})
set_target_properties(qhexedit PROPERTIES VERSION ${PROJECT_VERSION})

# executable
file(GLOB EXAMPLE_SOURCES "example/*.cpp")
file(GLOB EXAMPLE_HEADERS "example/*.h")
file(GLOB EXAMPLE_FORMS "example/*.ui")
file(GLOB EXAMPLE_TRANSLATIONS "example/translations/*.ts")

qt4_create_translation(EXAMPLE_QM_TRANSLATIONS ${EXAMPLE_TRANSLATIONS})
qt4_wrap_ui(EXAMPLE_FORMS ${EXAMPLE_FORMS})
qt4_add_resources(EXAMPLE_RESOURCES example/qhexedit.qrc)

add_executable(qhexedit-example ${EXAMPLE_SOURCES} ${EXAMPLE_HEADERS} ${EXAMPLE_FORMS} ${EXAMPLE_RESOURCES} ${EXAMPLE_TRANSLATIONS})
target_link_libraries(qhexedit-example qhexedit ${QT_QTCORE_LIBRARY} ${QT_QTGUI_LIBRARY})
set_target_properties(qhexedit-example PROPERTIES OUTPUT_NAME qhexedit)

### INSTALL ####################################################################
include(GNUInstallDirs)
set(LIBS_COMPONENT libs)
set(DEVEL_COMPONENT devel)
set(BINS_COMPONENT bins)

install(TARGETS qhexedit LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR} COMPONENT ${LIBS_COMPONENT})
install(FILES ${WIDGET_HEADERS} DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/qhexedit COMPONENT ${DEVEL_COMPONENT})
install(TARGETS qhexedit-example RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR} COMPONENT ${BINS_COMPONENT})
install(FILES ${EXAMPLE_QM_TRANSLATIONS} DESTINATION ${CMAKE_INSTALL_DATAROOTDIR}/qhexedit/translations COMPONENT ${BINS_COMPONENT})

### FOR PACKAGING ##############################################################
set(CPACK_PACKAGE_VERSION ${PROJECT_VERSION})
set(CPACK_PACKAGE_NAME ${PROJECT_NAME})
set(CPACK_PACKAGE_RELEASE 1)
set(CPACK_PACKAGE_VENDOR "R&EC SPb ETU")
set(CPACK_PACKAGE_FILE_NAME "${CPACK_PACKAGE_NAME}-${CPACK_PACKAGE_VERSION}-${CPACK_PACKAGE_RELEASE}.${CMAKE_SYSTEM_PROCESSOR}")
set(PACKAGE_REQUIRES "qt >= 4.6")

# rpm
set(CPACK_RPM_COMPONENT_INSTALL ON)
set(CPACK_RPM_PACKAGE_URL http://nicetu.spb.ru)
set(CPACK_RPM_PACKAGE_LICENSE GPL)

set(CPACK_RPM_PACKAGE_REQUIRES ${PACKAGE_REQUIRES})
set(CPACK_RPM_${BINS_COMPONENT}_PACKAGE_REQUIRES "${PACKAGE_REQUIRES} qhexedit2-${LIBS_COMPONENT}")
set(CPACK_RPM_${DEVEL_COMPONENT}_PACKAGE_REQUIRES "${PACKAGE_REQUIRES} qhexedit2-${LIBS_COMPONENT}")

include(CPack)
