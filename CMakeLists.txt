cmake_minimum_required(VERSION 3.5)

set(PROJECT_NAME cutefish-launcher)
project(${PROJECT_NAME})

set(CMAKE_INCLUDE_CURRENT_DIR ON)
set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

option(ENABLE_TRANSLATIONS "Build translations" ON)

set(QT Core Widgets DBus Quick QuickControls2 LinguistTools)
find_package(Qt5 REQUIRED ${QT})
find_package(KF5WindowSystem REQUIRED)
include(GNUInstallDirs)

set(SRCS
    src/desktopproperties.cpp
    src/iconthemeimageprovider.cpp
    src/launcher.cpp
    src/launchermodel.cpp
    src/appitem.cpp
    src/main.cpp
    src/pagemodel.cpp
    src/ucunits.cpp
    src/listmodelmanager.cpp
    src/iconitem.cpp
    src/processprovider.cpp
    src/appmanager.cpp
)

set(RESOURCES
    qml.qrc
)

qt5_add_dbus_adaptor(DBUS_SRCS
    src/com.cutefish.Launcher.xml
    src/launcher.h
    Launcher
    launcheradaptor
    LauncherAdaptor
)
set_source_files_properties(${DBUS_SRCS} PROPERTIES SKIP_AUTOGEN ON)

add_executable(${PROJECT_NAME} ${SRCS} ${DBUS_SRCS} ${RESOURCES})
target_link_libraries(${PROJECT_NAME}
        Qt5::Core
        Qt5::Widgets
        Qt5::DBus
        Qt5::Quick
        Qt5::QuickControls2
        KF5::WindowSystem
)

if (ENABLE_TRANSLATIONS AND Qt5_LUPDATE_EXECUTABLE)
    file(GLOB TS_FILES translations/*.ts)
    qt5_create_translation(QM_FILES ${TS_FILES})
    add_custom_target(translations DEPENDS ${QM_FILES} SOURCES ${TS_FILES})
    add_dependencies(${PROJECT_NAME} translations)
endif()

install(TARGETS ${PROJECT_NAME} RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR})
if (ENABLE_TRANSLATIONS AND Qt5_LUPDATE_EXECUTABLE)
    install(FILES ${QM_FILES} DESTINATION ${CMAKE_INSTALL_DATAROOTDIR}/${PROJECT_NAME}/translations)
endif()
