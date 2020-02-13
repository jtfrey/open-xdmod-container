# Config variables:
IF (NOT SINGULARITY_PREFIX)
    FIND_PROGRAM(SINGULARITY_EXE singularity)
    IF (SINGULARITY_EXE)
        GET_FILENAME_COMPONENT(_SINGULARITY_BINDIR ${SINGULARITY_EXE} DIRECTORY)
        GET_FILENAME_COMPONENT(_SINGULARITY_PREFIX ${_SINGULARITY_BINDIR} DIRECTORY)
        SET(SINGULARITY_PREFIX ${_SINGULARITY_PREFIX} CACHE STRING "Directory in which Singularity is installed")
    ELSE ()
        SET(SINGULARITY_PREFIX "/usr/local" CACHE STRING "Directory in which Singularity is installed")
        FIND_PROGRAM(SINGULARITY_EXE singularity HINTS ${SINGULARITY_PREFIX})
    ENDIF ()
ENDIF ()
SET(SINGULARITY_DNS_IPADDR "127.0.0.1" CACHE STRING "IP address of DNS server the container should use")
SET(SINGULARITY_HTTP_INSIDE "8080" CACHE STRING "TCP port Apache should use inside the container")
SET(SINGULARITY_HTTP_OUTSIDE "8080" CACHE STRING "TCP port to which in the inside port should be mapped")

# Generated content:
CONFIGURE_FILE(instance.in instance @ONLY)
IF (INSTALL_SINGULARITY_SYSTEMD_SERVICE)
    CONFIGURE_FILE(Singularity/xdmod.service.in xdmod.service @ONLY)
ENDIF ()

# If the singularity command was present, we can try to build the container image:
IF (SINGULARITY_EXE)
    # Build the Singularity container:
    ADD_CUSTOM_COMMAND(OUTPUT XDMoD.sif
            COMMAND sudo ${SINGULARITY_EXE} build ${CMAKE_CURRENT_BINARY_DIR}/XDMoD.sif Singularity
            WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/Singularity
            COMMENT "Building Singularity container..."
            DEPENDS Singularity/Singularity Singularity/XDMoD-start
        )
    ADD_CUSTOM_TARGET(XDMoD-container ALL DEPENDS XDMoD.sif)
ELSE ()
    MESSAGE("The 'singularity' command could not be found, so you will need to build the container image manually.")
ENDIF ()

# Install:
INSTALL(PROGRAMS ${CMAKE_CURRENT_BINARY_DIR}/instance DESTINATION ${CMAKE_INSTALL_SBINDIR})
IF (INSTALL_SINGULARITY_SYSTEMD_SERVICE)
    INSTALL(FILES ${CMAKE_CURRENT_BINARY_DIR}/xdmod.service DESTINATION /etc/systemd/system)
ENDIF ()
INSTALL(FILES ${CMAKE_CURRENT_BINARY_DIR}/XDMoD.sif DESTINATION ${CMAKE_INSTALL_SYSCONFDIR})

