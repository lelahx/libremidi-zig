cmake_minimum_required(VERSION 3.22 FATAL_ERROR)
project(libremidi
  VERSION 5.2.0
  DESCRIPTION "A cross-platform MIDI library"
  LANGUAGES C CXX
  HOMEPAGE_URL "https://github.com/jcelerier/libremidi"
)

list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake")

include(CMakeDependentOption)
include(CheckSymbolExists)
include(CheckCXXCompilerFlag)
include(CheckCXXSourceCompiles)
include(CheckIncludeFileCXX)
include(FetchContent)
include(GNUInstallDirs)

### Options ###
option(LIBREMIDI_HEADER_ONLY "Header-only mode" OFF)

cmake_dependent_option(LIBREMIDI_NO_COREMIDI "Disable CoreMidi back-end" OFF "APPLE" OFF)
cmake_dependent_option(LIBREMIDI_NO_WINMM "Disable WinMM back-end" OFF "WIN32" OFF)
cmake_dependent_option(LIBREMIDI_NO_WINUWP "Disable UWP back-end" OFF "WIN32" OFF)
cmake_dependent_option(LIBREMIDI_NO_WINMIDI "Disable WinMIDI back-end" OFF "WIN32" OFF)
# if(LINUX) in CMake 3.25
cmake_dependent_option(LIBREMIDI_NO_ALSA "Disable ALSA back-end" OFF "UNIX; NOT APPLE" OFF)
cmake_dependent_option(LIBREMIDI_NO_UDEV "Disable udev support for ALSA" OFF "UNIX; NOT APPLE" OFF)
option(LIBREMIDI_NO_JACK "Disable JACK back-end" OFF)
option(LIBREMIDI_NO_PIPEWIRE "Disable PipeWire back-end" OFF)
option(LIBREMIDI_NO_NETWORK "Disable Network back-end" OFF)
option(LIBREMIDI_NO_KEYBOARD "Disable Computer keyboard back-end" OFF)

option(LIBREMIDI_NO_EXPORTS "Disable dynamic symbol exporting" OFF)
option(LIBREMIDI_NO_BOOST "Do not use Boost if available" OFF)
option(LIBREMIDI_SLIM_MESSAGE "Use a fixed-size message format" 0)
option(LIBREMIDI_FIND_BOOST "Actively look for Boost" OFF)
option(LIBREMIDI_EXAMPLES "Enable examples" OFF)
option(LIBREMIDI_TESTS "Enable tests" OFF)
option(LIBREMIDI_NI_MIDI2 "Enable compatibility with ni-midi2" OFF)
option(LIBREMIDI_CI "To be enabled only in CI, some tests cannot run there. Also enables -Werror." OFF)

cmake_dependent_option(LIBREMIDI_NO_WARNINGS "Disables warnings from library compilation" OFF "NOT LIBREMIDI_HEADER_ONLY" ON)

### Basic setup ###
set(CMAKE_INCLUDE_CURRENT_DIR ON)
set(CMAKE_POSITION_INDEPENDENT_CODE ON)

if(NOT CMAKE_CXX_STANDARD OR ("${CMAKE_CXX_STANDARD}" VERSION_LESS 20))
  set(CMAKE_CXX_STANDARD 20)
endif()

### Dependencies ###
include(libremidi.deps)

### Main library ###
include(libremidi.library)

### Backends ###
include(libremidi.emscripten)
include(libremidi.win32)
include(libremidi.macos)
include(libremidi.alsa)
include(libremidi.jack)
include(libremidi.pipewire)
include(libremidi.keyboard)
include(libremidi.net)

### Install ###
include(libremidi.install)

### Examples ###
if(LIBREMIDI_EXAMPLES)
  message(STATUS "libremidi: compiling examples")
  include(libremidi.examples)
endif()

### Tests ###
if(LIBREMIDI_TESTS)
  message(STATUS "libremidi: compiling tests")
  include(libremidi.tests)
endif()
