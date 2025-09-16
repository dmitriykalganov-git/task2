cmake_minimum_required(VERSION 3.5)

project(ip_filter)

#option(WITH_BOOST_TEST "Whether to build Boost test" ON)

add_executable(ip_filter main.cpp)
add_definitions("-Wno-unused-parameter")
set_target_properties(
	ip_filter PROPERTIES
	CXX_STANDARD 20
	CXX_STANDARD_REQUIRED ON
)

target_include_directories(ip_filter
    PRIVATE "${CMAKE_BINARY_DIR}"
)

set(Boost_INCLUDE_DIR "/usr/include/boost")
set(Boost_LIBRARIES "/usr/lib/x86_64-linux-gnu")

if(WITH_BOOST_TEST)
    find_package(Boost COMPONENTS unit_test_framework REQUIRED)
    add_executable(test_calc main.cpp)

    set_target_properties(test_calc PROPERTIES
        CXX_STANDARD 20
        CXX_STANDARD_REQUIRED ON
    )

    set_target_properties(test_calc PROPERTIES
        COMPILE_DEFINITIONS BOOST_TEST_DYN_LINK
        INCLUDE_DIRECTORIES ${Boost_INCLUDE_DIR}
    )
    
    target_link_libraries(test_calc ${Boost_LIBRARIES})
#    target_link_libraries(test_version  ${Boost_LIBRARIES})
    message(STATUS "Found BOOST ${Boost_VERSION_STRING}")
  #  target_link_libraries(test_calc
   #     ${Boost_LIBRARIES}
   #     Calculator
    #)
endif()

if (MSVC)
    target_compile_options(ip_filter PRIVATE
        /W4
    )

    if(WITH_BOOST_TEST)
        target_compile_options(test_calc PRIVATE
            /W4
        )
    endif()
else ()
    target_compile_options(ip_filter PRIVATE
        -Wall -Wextra -pedantic -Werror
    )

    if(WITH_BOOST_TEST)
        target_compile_options(test_calc PRIVATE
            -Wall -Wextra -pedantic -Werror
        )
    endif()
endif()

install(TARGETS ip_filter RUNTIME DESTINATION bin)

set(CPACK_GENERATOR DEB)

include(CPack)

if(WITH_BOOST_TEST)
    enable_testing()
    add_test(test_calc test_calc)
endif()