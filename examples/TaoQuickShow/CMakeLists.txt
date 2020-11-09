get_filename_component(TargetName ${CMAKE_CURRENT_SOURCE_DIR} NAME)


find_package(QT NAMES Qt6 Qt5 COMPONENTS Core Quick QuickControls2 REQUIRED)
find_package(Qt${QT_VERSION_MAJOR} COMPONENTS Core Quick QuickControls2 REQUIRED)
if(Qt${QT_VERSION_MAJOR}Core_Found)
    if (Qt::Core_VERSION VERSION_LESS 5.9.0)
        message(FATAL_ERROR "TaoQuick minimum supported Qt5 version is 5.9.0")
    else ()
        message("Qt::Core_VERSION ${Qt::Core_VERSION}")
    endif()
endif(Qt::Core_Found)

aux_source_directory(./Src SRC1)
aux_source_directory(./Src/DeviceAddTable SRC2)
include(taoVersion)

add_compile_definitions(TaoCommon_NO_LIB)
add_subdirectory(${CMAKE_SOURCE_DIR}/3rdparty ${CMAKE_SOURCE_DIR}/build/3rdparty)
message("TaoCommonPath ${TaoCommonPath}")
message("TaoCommonFiles ${TaoCommonFiles}")

include(taoQuick)
message("TaoQuickRes ${TaoQuickRes}")
set (Res Qml.qrc Contents.qrc Image.qrc)
if (CMAKE_BUILD_TYPE MATCHES "Release")
    if(WIN32)
        add_executable(${TargetName}
            ${SRC1}
            ${SRC2}
            ${TaoCommonFiles}
            ${Res}
#            App.rc
            ${TaoQuickRes}
            )
    elseif(APPLE)
        add_executable(${TargetName}
            ${SRC1}
            ${SRC2}
            ${TaoCommonFiles}
            ${Res}
            milk.icns
            ${TaoQuickRes}
            )
    else()
        add_executable(${TargetName}
            ${SRC1}
            ${SRC2}
            ${TaoCommonFiles}
            ${Res}
            ${TaoQuickRes}
            )
    endif()
    add_compile_definitions(qmlPath="qrc:/Qml/")
    add_compile_definitions(contentsPath="qrc:/Contents/")
    add_compile_definitions(imgPath="qrc:/Image/")

else()
    add_executable(${TargetName}
        ${SRC1}
        ${SRC2}
        ${TaoCommonFiles}
        )
    add_compile_definitions(TAODEBUG)
    add_compile_definitions(qmlPath="file:///${CMAKE_CURRENT_SOURCE_DIR}/Qml/")
    add_compile_definitions(contentsPath="file:///${CMAKE_CURRENT_SOURCE_DIR}/Contents/")
    add_compile_definitions(imgPath="file:///${CMAKE_CURRENT_SOURCE_DIR}/Image/")
endif()

target_include_directories(${TargetName} PUBLIC . ./Src ${TaoCommonPath})

target_compile_definitions(${TargetName}
    PRIVATE $<$<OR:$<CONFIG:Debug>,$<CONFIG:RelWithDebInfo>>:QT_QML_DEBUG>)
target_link_libraries(${TargetName}
    PRIVATE Qt${QT_VERSION_MAJOR}::Core Qt${QT_VERSION_MAJOR}::Quick Qt${QT_VERSION_MAJOR}::QuickControls2)

configure_file(${CMAKE_CURRENT_SOURCE_DIR}/Trans/language_zh.json ${EXECUTABLE_OUTPUT_PATH}/Trans/language_zh.json COPYONLY)