cmake_minimum_required(VERSION 3.14)

project("ElectionGuard SDK" VERSION 0.0.1 LANGUAGES C)

enable_testing()

# Allow us to import cmake scripts from  ./cmake
set(CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}/cmake")

set(CMAKE_C_EXTENSIONS OFF)

message("Generated with config types: ${CMAKE_CONFIGURATION_TYPES}")

IF(CMAKE_BUILD_TYPE MATCHES Debug)
    message("setting DEBUG during compile")
    add_compile_definitions(DEBUG)
    #add_compile_definitions(DEBUG_PRINT)
ENDIF()


add_library(electionguard
    ${PROJECT_SOURCE_DIR}/src/electionguard/api/base_hash.c
    ${PROJECT_SOURCE_DIR}/src/electionguard/api/filename.c
    ${PROJECT_SOURCE_DIR}/src/electionguard/api/create_election.c
    ${PROJECT_SOURCE_DIR}/src/electionguard/api/encrypt_ballot.c
    ${PROJECT_SOURCE_DIR}/src/electionguard/api/load_ballots.c
    ${PROJECT_SOURCE_DIR}/src/electionguard/api/record_ballots.c
    ${PROJECT_SOURCE_DIR}/src/electionguard/api/tally_votes.c
    ${PROJECT_SOURCE_DIR}/src/electionguard/crypto_reps.h
    ${PROJECT_SOURCE_DIR}/src/electionguard/voting/ballot_collection.c
    ${PROJECT_SOURCE_DIR}/src/electionguard/voting/coordinator.c
    ${PROJECT_SOURCE_DIR}/src/electionguard/voting/messages.c
    ${PROJECT_SOURCE_DIR}/src/electionguard/voting/message_reps.h
    ${PROJECT_SOURCE_DIR}/src/electionguard/voting/nouns.c
    ${PROJECT_SOURCE_DIR}/src/electionguard/voting/tracker.c
    ${PROJECT_SOURCE_DIR}/src/electionguard/voting/nouns.h
    ${PROJECT_SOURCE_DIR}/src/electionguard/voting/encrypter.c
    ${PROJECT_SOURCE_DIR}/src/electionguard/serialize/builtins.c
    ${PROJECT_SOURCE_DIR}/src/electionguard/serialize/trustee_state.h
    ${PROJECT_SOURCE_DIR}/src/electionguard/serialize/state.c
    ${PROJECT_SOURCE_DIR}/src/electionguard/serialize/keyceremony.h
    ${PROJECT_SOURCE_DIR}/src/electionguard/serialize/decryption.c
    ${PROJECT_SOURCE_DIR}/src/electionguard/serialize/voting.h
    ${PROJECT_SOURCE_DIR}/src/electionguard/serialize/crypto.h
    ${PROJECT_SOURCE_DIR}/src/electionguard/serialize/builtins.h
    ${PROJECT_SOURCE_DIR}/src/electionguard/serialize/trustee_state.c
    ${PROJECT_SOURCE_DIR}/src/electionguard/serialize/voting.c
    ${PROJECT_SOURCE_DIR}/src/electionguard/serialize/decryption.h
    ${PROJECT_SOURCE_DIR}/src/electionguard/serialize/keyceremony.c
    ${PROJECT_SOURCE_DIR}/src/electionguard/serialize/state.h
    ${PROJECT_SOURCE_DIR}/src/electionguard/serialize/crypto.c
    ${PROJECT_SOURCE_DIR}/src/electionguard/decryption/coordinator.c
    ${PROJECT_SOURCE_DIR}/src/electionguard/decryption/message_reps.h
    ${PROJECT_SOURCE_DIR}/src/electionguard/decryption/trustee.c
    ${PROJECT_SOURCE_DIR}/src/electionguard/keyceremony/coordinator.c
    ${PROJECT_SOURCE_DIR}/src/electionguard/keyceremony/message_reps.h
    ${PROJECT_SOURCE_DIR}/src/electionguard/keyceremony/trustee.c
    ${PROJECT_SOURCE_DIR}/src/electionguard/uint4096.c
    ${PROJECT_SOURCE_DIR}/src/electionguard/bignum.c
    ${PROJECT_SOURCE_DIR}/src/electionguard/log.h
    ${PROJECT_SOURCE_DIR}/src/electionguard/sha2-openbsd.c
    ${PROJECT_SOURCE_DIR}/src/electionguard/sha2-openbsd.h
    ${PROJECT_SOURCE_DIR}/src/electionguard/crypto.c
    ${PROJECT_SOURCE_DIR}/src/electionguard/secure_zero_memory.c
    ${PROJECT_SOURCE_DIR}/src/electionguard/rsa.c
    ${PROJECT_SOURCE_DIR}/src/electionguard/random_source.h
    ${PROJECT_SOURCE_DIR}/src/electionguard/random_source.c
    ${PROJECT_SOURCE_DIR}/src/electionguard/trustee_state_rep.h
    ${PROJECT_SOURCE_DIR}/src/electionguard/directory.c
    ${PROJECT_SOURCE_DIR}/src/electionguard/directory.h
    ${PROJECT_SOURCE_DIR}/src/electionguard/uthash.h
    ${PROJECT_SOURCE_DIR}/include/electionguard/api/config.h
    ${PROJECT_SOURCE_DIR}/include/electionguard/api/create_election.h
    ${PROJECT_SOURCE_DIR}/include/electionguard/api/encrypt_ballot.h
    ${PROJECT_SOURCE_DIR}/include/electionguard/api/load_ballots.h
    ${PROJECT_SOURCE_DIR}/include/electionguard/api/record_ballots.h
    ${PROJECT_SOURCE_DIR}/include/electionguard/api/tally_votes.h
    ${PROJECT_SOURCE_DIR}/include/electionguard/max_values.h
    ${PROJECT_SOURCE_DIR}/include/electionguard/trustee_state.h
    ${PROJECT_SOURCE_DIR}/include/electionguard/voting/messages.h
    ${PROJECT_SOURCE_DIR}/include/electionguard/voting/encrypter.h
    ${PROJECT_SOURCE_DIR}/include/electionguard/voting/coordinator.h
    ${PROJECT_SOURCE_DIR}/include/electionguard/voting/tracker.h
    ${PROJECT_SOURCE_DIR}/include/electionguard/decryption/messages.h
    ${PROJECT_SOURCE_DIR}/include/electionguard/decryption/coordinator.h
    ${PROJECT_SOURCE_DIR}/include/electionguard/decryption/trustee.h
    ${PROJECT_SOURCE_DIR}/include/electionguard/crypto.h
    ${PROJECT_SOURCE_DIR}/include/electionguard/rsa.h
    ${PROJECT_SOURCE_DIR}/include/electionguard/keyceremony/messages.h
    ${PROJECT_SOURCE_DIR}/include/electionguard/keyceremony/coordinator.h
    ${PROJECT_SOURCE_DIR}/include/electionguard/keyceremony/trustee.h
)

# Compiler flags
if(CMAKE_CXX_COMPILER_ID MATCHES "Clang|GNU")
    target_compile_options(electionguard PUBLIC -Werror -Wall -Wextra -pedantic -pedantic-errors -Wunreachable-code -Wmissing-field-initializers -lgmp)
endif()
if(CMAKE_CXX_COMPILER_ID MATCHES "Clang")
    target_compile_options(electionguard PUBLIC -Werror --fsanitize=address -Wgnu-empty-initializer)
endif()
if(CMAKE_CXX_COMPILER_ID MATCHES "GNU")
    target_compile_options(electionguard PUBLIC -Werror -Wenum-compare)
endif()

# Link gmp library
find_package(GMP REQUIRED)
target_link_libraries(electionguard ${GMP_LIBRARY})

if (MINGW)
    # Link BCrypt
    target_link_libraries(electionguard BCrypt)
    # Remove lib prefix
    set_target_properties(electionguard PROPERTIES PREFIX "")
endif()

# Set the public include directory depending on 
# if the target is being exported or installed
target_include_directories(electionguard
    SYSTEM PUBLIC
        $<INSTALL_INTERFACE:include>
        $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/include>
        $<BUILD_INTERFACE:${GMP_INCLUDE_DIR}>
    PRIVATE
        ${PROJECT_SOURCE_DIR}/src/electionguard
)

# Install the electionguard library in the default location, and associate
# electionguard with the ElectionGuard export
install(TARGETS electionguard EXPORT ElectionGuard)

# Install public header files
install(
    DIRECTORY ${PROJECT_SOURCE_DIR}/include/electionguard
    TYPE INCLUDE
    FILES_MATCHING PATTERN "*.h*"
)

# Generate the build-tree ElectionGuardConfig.cmake for use in other cmake
# projects without needing to install
export(
    EXPORT ElectionGuard
    FILE "${CMAKE_CURRENT_BINARY_DIR}/ElectionGuard/ElectionGuardConfig.cmake"
)

# Generate the install-tree ElectionGuardConfig.cmake for use in other cmake
# projects after this library has been installed
install(
    EXPORT ElectionGuard
    FILE ElectionGuardConfig.cmake
    DESTINATION lib/cmake/ElectionGuard
)

add_subdirectory(docs)

file(MAKE_DIRECTORY  "${CMAKE_CURRENT_BINARY_DIR}/api_build")
set(CMAKE_CONFIGURATION_TYPES "Debug" CACHE STRING "" FORCE)

add_test(NAME build_api
   COMMAND "${CMAKE_COMMAND}"
      -E env ElectionGuard_DIR=${CMAKE_CURRENT_BINARY_DIR}/ElectionGuard
      "${CMAKE_CTEST_COMMAND}"
        --build-and-test "${PROJECT_SOURCE_DIR}/examples/api" "${CMAKE_CURRENT_BINARY_DIR}/api_build"
        --build-generator ${CMAKE_GENERATOR}
        --test-command api
)

include(CheckIncludeFiles)
check_include_files("windows.h;bcrypt.h" HAVE_BCRYPTGENRANDOM)
configure_file(${PROJECT_SOURCE_DIR}/src/electionguard/random_source.h.in ${PROJECT_SOURCE_DIR}/src/electionguard/random_source.h)
