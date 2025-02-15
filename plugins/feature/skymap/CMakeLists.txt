project(skymap)

set(skymap_SOURCES
    skymap.cpp
    skymapsettings.cpp
    skymapplugin.cpp
    skymapwebapiadapter.cpp
    webserver.cpp
)

set(skymap_HEADERS
    skymap.h
    skymapsettings.h
    skymapplugin.h
    skymapreport.h
    skymapwebapiadapter.h
    webserver.h
)

include_directories(
    ${CMAKE_SOURCE_DIR}/swagger/sdrangel/code/qt5/client
    ${Qt${QT_DEFAULT_MAJOR_VERSION}Gui_PRIVATE_INCLUDE_DIRS}
)

if(NOT SERVER_MODE)
    set(skymap_SOURCES
        ${skymap_SOURCES}
        skymapgui.cpp
        skymapgui.ui
        skymapsettingsdialog.cpp
        skymapsettingsdialog.ui
        websocketserver.cpp
        skymap.qrc
        webinterface.cpp
        wtml.cpp
        webview.cpp
        icons.qrc
    )
    set(skymap_HEADERS
        ${skymap_HEADERS}
        skymapgui.h
        skymapsettingsdialog.h
        skymapmodel.h
        skymapitem.h
        websocketserver.h
        skymaptileserver.h
        webinterface.h
        wtml.h
        webview.h
    )

    set(TARGET_NAME featureskymap)
    set(TARGET_LIB_GUI "sdrgui")
    set(INSTALL_FOLDER ${INSTALL_PLUGINS_DIR})

    if(Qt${QT_DEFAULT_MAJOR_VERSION}WebEngineCore_FOUND)
        set(TARGET_LIB "Qt::Widgets" Qt::Positioning Qt::Location Qt::WebEngineCore Qt::WebEngineWidgets)
    elseif(Qt${QT_DEFAULT_MAJOR_VERSION}WebEngine_FOUND)
        set(TARGET_LIB "Qt::Widgets" Qt::Positioning Qt::Location Qt::WebEngine Qt::WebEngineCore Qt::WebEngineWidgets)
    else()
        set(TARGET_LIB "Qt::Widgets" Qt::Positioning Qt::Location)
    endif()
else()
    set(TARGET_NAME featureskymapsrv)
    set(TARGET_LIB "")
    set(TARGET_LIB_GUI "")
    set(INSTALL_FOLDER ${INSTALL_PLUGINSSRV_DIR})
endif()

add_library(${TARGET_NAME} SHARED
    ${skymap_SOURCES}
)

target_link_libraries(${TARGET_NAME}
    Qt::Core
    ${TARGET_LIB}
    sdrbase
    ${TARGET_LIB_GUI}
)

install(TARGETS ${TARGET_NAME} DESTINATION ${INSTALL_FOLDER})

if(WIN32)
  # Run deployqt for QtQuick etc
  include(DeployQt)
  windeployqt(${TARGET_NAME} ${SDRANGEL_BINARY_BIN_DIR} ${PROJECT_SOURCE_DIR}/skymap)
endif()

# Install debug symbols
if (WIN32)
    install(FILES $<TARGET_PDB_FILE:${TARGET_NAME}> CONFIGURATIONS Debug RelWithDebInfo DESTINATION ${INSTALL_FOLDER} )
endif()
