cmake_minimum_required(VERSION 3.0)

include(ExternalProject)

find_program(MAKE_EXECUTABLE
             NAMES make)

set(DPDK_PATH ${CMAKE_SOURCE_DIR}/dpdk)
SET(DPDK_BUILD_DIR ${CMAKE_BINARY_DIR}/dpdk/build)
SET(ENV{RTE_OUTPUT} ${DPDK_BUILD_DIR})

IF(NOT EXISTS ${DPDK_PATH}/.git)
	MESSAGE("Submodule not initialized: dpdk")
	EXECUTE_PROCESS(
		COMMAND sh -c     "git submodule update --init"
		WORKING_DIRECTORY ${DPDK_PATH}
	)
ENDIF()

configure_file(${CMAKE_SOURCE_DIR}/rte.config ${DPDK_BUILD_DIR}/.config)

ExternalProject_Add(
	dpdk
	PREFIX ${DPDK_BUILD_DIR}
	SOURCE_DIR ${DPDK_PATH}
	CONFIGURE_COMMAND ""
	BINARY_DIR ${DPDK_BUILD_DIR}
	BUILD_COMMAND ${MAKE_EXECUTABLE} -C ${DPDK_PATH} O=${DPDK_BUILD_DIR}
	INSTALL_COMMAND ""
)

INCLUDE_DIRECTORIES(${DPDK_BUILD_DIR}/include)
LINK_DIRECTORIES(${DPDK_BUILD_DIR}/lib)
