cmake_minimum_required(VERSION 3.22)
project(PointilSynthTests VERSION 0.1.0)

enable_testing()
include(GoogleTest) # For GTest::gtest_main and gtest_discover_tests

set(TEST_SOURCE_FILES
    source/AudioProcessorTest.cpp
    source/PresetManagerTest.cpp
)

juce_add_console_app(${PROJECT_NAME}
    #SOURCES ${TEST_SOURCE_FILES}
    # Note: If TARGET_LIBRARIES is not a valid keyword for juce_add_console_app,
    # this will cause an error, and linking will need to be done via
    # a separate target_link_libraries command.
    #TARGET_LIBRARIES PointillisticSynth GTest::gtest_main
    PRODUCT_NAME "PointilSynth Tests"
    MODULES
    juce_core
    juce_events
    juce_gui_basics # For DebugUIPanel and ScopedJuceInitialiser_GUI
    juce_audio_processors # For APVTS in PanelTestFixtureA
    juce_audio_basics
)

target_sources(${PROJECT_NAME}
  PRIVATE
    ${TEST_SOURCE_FILES}
)

target_link_libraries(${PROJECT_NAME}
    PRIVATE
        PointillisticSynth_SharedCode
        juce::juce_core
        juce::juce_audio_processors
        juce::juce_audio_basics
        juce::juce_dsp
        juce::juce_gui_basics
        nlohmann_json::nlohmann_json
        GTest::gtest_main
)

target_include_directories(PointilSynthTests
    PRIVATE
        ${CMAKE_SOURCE_DIR}/Source
        ${CMAKE_SOURCE_DIR}/plugin/source
        ${CMAKE_SOURCE_DIR}/plugin/include
        ${CMAKE_CURRENT_SOURCE_DIR}/source   # your test sources
)

# Removed: set_source_files_properties(${SOURCE_FILES} PROPERTIES COMPILE_OPTIONS "${PROJECT_WARNINGS_CXX}")
# JUCE toolchain should handle appropriate warnings.

if(CMAKE_GENERATOR STREQUAL Xcode)
  gtest_discover_tests(${PROJECT_NAME} DISCOVERY_MODE PRE_TEST)
else()
  gtest_discover_tests(${PROJECT_NAME})
endif()
