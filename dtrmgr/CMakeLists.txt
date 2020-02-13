#
# Rather than bundling the code, we fetch it from github.
#

INCLUDE(FetchContent)

FETCHCONTENT_DECLARE(
        dtrmgr
        GIT_REPOSITORY  https://github.com/jtfrey/dtrmgr.git
        GIT_TAG v0.1.0
    )

FETCHCONTENT_GETPROPERTIES(dtrmgr)
IF (NOT dtrmgr_POPULATED)
	FETCHCONTENT_POPULATE(dtrmgr)
	ADD_SUBDIRECTORY(${dtrmgr_SOURCE_DIR} ${dtrmgr_BINARY_DIR})
ENDIF()