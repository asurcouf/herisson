if (IS_GIT_REPO)
   execute_process( COMMAND git log -10 "--pretty='%h - %an, %ar : %s \t'" OUTPUT_VARIABLE GIT_LAST_10_COMMITS )
   execute_process( COMMAND git rev-parse --abbrev-ref HEAD OUTPUT_VARIABLE GIT_CURRENTBRANCH)

   string(REGEX REPLACE "\n" " " GIT_LAST_10_COMMITS "${GIT_LAST_10_COMMITS}")
   string(REGEX REPLACE "\n" " " GIT_CURRENTBRANCH "${GIT_CURRENTBRANCH}")

   file(WRITE "${CMAKE_CURRENT_BINARY_DIR}/gitversion.cpp" "const char *gitlast10commits = \"${GIT_LAST_10_COMMITS}\";\n")
   file(APPEND "${CMAKE_CURRENT_BINARY_DIR}/gitversion.cpp" "const char *gitcurrentbranch = \"${GIT_CURRENTBRANCH}\";\n")
   
   set(GIT_VERSION_FILE "${CMAKE_CURRENT_BINARY_DIR}/gitversion.cpp")
else()
   set(GIT_VERSION_FILE "")
endif()

add_executable(vMI_adapter vMI_adapter.cpp ${GIT_VERSION_FILE})
target_link_libraries(vMI_adapter PRIVATE vMI)
target_include_directories(vMI_adapter PRIVATE "${CMAKE_CURRENT_SOURCE_DIR}/../common/")

add_executable(vMI_demuxer vMI_demuxer.cpp ${GIT_VERSION_FILE})
target_link_libraries(vMI_demuxer PRIVATE vMI)
target_include_directories(vMI_demuxer PRIVATE "${CMAKE_CURRENT_SOURCE_DIR}/../common/")

add_executable(vMI_converter vMI_converter.cpp ${GIT_VERSION_FILE})
target_link_libraries(vMI_converter PRIVATE vMI)
target_include_directories(vMI_converter PRIVATE "${CMAKE_CURRENT_SOURCE_DIR}/../common/")

if (HAVE_PNG)
    add_executable(vMI_imageinsertor vMI_imageinsertor.cpp logo.cpp ${CMAKE_CURRENT_SOURCE_DIR}/../common/pngtools.cpp ${GIT_VERSION_FILE})
    target_link_libraries(vMI_imageinsertor PRIVATE vMI)
    target_link_libraries(vMI_imageinsertor PRIVATE ${PNG_LIBRARIES} ${ZLIB_LIBRARIES})
    target_include_directories(vMI_imageinsertor PRIVATE "${CMAKE_CURRENT_SOURCE_DIR}/../common/")
    target_include_directories(vMI_imageinsertor PRIVATE ${PNG_INCLUDE_DIRS} ${ZLIB_INCLUDE_DIRS})
endif()

add_executable(vMI_frameretarder vMI_frameretarder.cpp ${GIT_VERSION_FILE})
target_link_libraries(vMI_frameretarder PRIVATE vMI)
target_include_directories(vMI_frameretarder PRIVATE "${CMAKE_CURRENT_SOURCE_DIR}/../common/")

if (HAVE_X11)
    add_executable(vMI_videoplayer vMI_videoplayer.cpp ${GIT_VERSION_FILE})
    target_link_libraries(vMI_videoplayer PRIVATE vMI)
    target_link_libraries(vMI_videoplayer PRIVATE X11)
    target_include_directories(vMI_videoplayer PRIVATE "${CMAKE_CURRENT_SOURCE_DIR}/../common/")
endif()

add_executable(vMI_synchronizer vMI_synchronizer.cpp ${GIT_VERSION_FILE})
target_link_libraries(vMI_synchronizer PRIVATE vMI)
target_include_directories(vMI_synchronizer PRIVATE "${CMAKE_CURRENT_SOURCE_DIR}/../common/")

if (WIN32)
	add_executable(vMI_winvideoplayer vMI_winvideoplayer.cpp ${GIT_VERSION_FILE})
	target_link_libraries(vMI_winvideoplayer PRIVATE vMI ${OPENCV2_LIBRARIES})
	target_include_directories(vMI_winvideoplayer PRIVATE "${CMAKE_CURRENT_SOURCE_DIR}/../common/" ${OPENCV2_INCLUDE_DIRS})
endif()


