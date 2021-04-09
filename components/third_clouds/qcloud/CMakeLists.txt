if (CONFIG_AT_QCLOUD_IOT_COMMAND_SUPPORT)
    execute_process(COMMAND git rev-parse --short HEAD
    OUTPUT_VARIABLE COMMIT_ID
    OUTPUT_STRIP_TRAILING_WHITESPACE)

    add_compile_options("-DQCLOUD_AT_COMMIT_ID=\"${COMMIT_ID}\"")
    add_compile_options("-DENABLE_TEST_COMMANDS")

    execute_process(COMMAND git describe --tag
    WORKING_DIRECTORY $ENV{IDF_PATH}
    OUTPUT_VARIABLE SDK_VERSION
    OUTPUT_STRIP_TRAILING_WHITESPACE)
    add_compile_options("-DQCLOUD_AT_SDK_VERSION=\"${SDK_VERSION}\"")
endif()

    idf_component_register (
        INCLUDE_DIRS include)

    if (${SILENCE} EQUAL 1)
    set(LIB_NAME _qcloud_core_silence)
    else()
    set(LIB_NAME _qcloud_core)
    endif()

    string(SUBSTRING "$ENV{ESP_AT_PROJECT_PLATFORM}" 9 31 PLATFORM_NAME) # remove PLATFORM_
    string(STRIP ${PLATFORM_NAME} PLATFORM_NAME)
    string(CONCAT FULL_NAME ${PLATFORM_NAME} ${LIB_NAME})
    string(TOLOWER ${FULL_NAME} LIBS)

    message("silence:${SILENCE}, ld core:${CMAKE_CURRENT_SOURCE_DIR}/lib/lib${LIBS}.a")

    add_library(${LIBS} STATIC IMPORTED)
    set_property(TARGET ${LIBS} PROPERTY IMPORTED_LOCATION ${CMAKE_CURRENT_SOURCE_DIR}/lib/lib${LIBS}.a)
    target_link_libraries(${COMPONENT_LIB} INTERFACE ${LIBS})
    set_property(TARGET ${LIBS} APPEND PROPERTY INTERFACE_LINK_LIBRARIES ${COMPONENT_LIB})