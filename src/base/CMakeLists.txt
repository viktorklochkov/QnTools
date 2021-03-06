project(QnToolsBase)
set(SOURCES QVector.cpp DataContainerHelper.cpp Stats.cpp ReSamples.cpp)
set(HEADERS DataContainer.hpp DataContainerHelper.hpp Axis.hpp QVector.hpp Stats.hpp Cuts.hpp Statistic.hpp ReSamples.hpp)

add_custom_command(OUTPUT G__${PROJECT_NAME}.cxx
        COMMAND ${ROOTCLING} -v -f G__${PROJECT_NAME}.cxx
        -rml lib${PROJECT_NAME}${CMAKE_SHARED_LIBRARY_SUFFIX}
        -rmf lib${PROJECT_NAME}.rootmap
        -s lib${PROJECT_NAME}
        -I${CMAKE_CURRENT_SOURCE_DIR}
        ${CMAKE_CURRENT_SOURCE_DIR}/${HEADERS}
        ${CMAKE_CURRENT_SOURCE_DIR}/${PROJECT_NAME}LinkDef.h
        )
add_library(${PROJECT_NAME} SHARED ${SOURCES} G__${PROJECT_NAME}.cxx)
add_library(QnTools::Base ALIAS ${PROJECT_NAME})

target_compile_definitions(${PROJECT_NAME} PUBLIC "-DUSE_ROOT")
target_include_directories(${PROJECT_NAME}
        PRIVATE
        ${ROOT_INCLUDE_DIRS}
        ${CMAKE_CURRENT_SOURCE_DIR}
        PUBLIC
        $<INSTALL_INTERFACE:include/QnTools>
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
        )
target_link_libraries(${PROJECT_NAME} PUBLIC ${ROOT_LIBRARIES})

# link to the gtest library if test are enabled
IF (CMAKE_BUILD_TYPE MATCHES Debug)
    list(APPEND SOURCES QVector.test.cpp)
    add_executable(${PROJECT_NAME}UnitTests ${SOURCES})
    target_link_libraries(${PROJECT_NAME}UnitTests PRIVATE gtest_main PUBLIC ${PROJECT_NAME})
    gtest_add_tests(TARGET ${PROJECT_NAME}UnitTests)
ENDIF(CMAKE_BUILD_TYPE MATCHES Debug)