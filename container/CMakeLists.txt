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

# Generated content:
CONFIGURE_FILE(Singularity/xdmod.service.in xdmod.service @ONLY)

# If the singularity command was present, we can try to build the container image:
IF (SINGULARITY_EXE)
    # Build the Singularity container:
    ADD_CUSTOM_TARGET(XDMod.sif ALL ${SINGULARITY_EXE} build XDMod.sif Singularity
            WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/Singularity
            COMMENT "Building Singularity container..."
            DEPENDS XDMod.sif
        )
ELSE ()
    MESSAGE("The 'singularity' command could not be found, so you will need to build the container image manually.")
ENDIF ()

# Install:
INSTALL(PROGRAMS ${CMAKE_CURRENT_BINARY_DIR}/instance DESTINATION ${CMAKE_INSTALL_SBINDIR})
INSTALL(FILES ${CMAKE_CURRENT_BINARY_DIR}/xdmod.service DESTINATION /etc/systemd/system)

