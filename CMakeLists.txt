CMAKE_MINIMUM_REQUIRED (VERSION 3.11)
PROJECT (open-xdmod-container VERSION 2019.0 LANGUAGES C)

# Must use GNUInstallDirs to install libraries into correct
# locations on all platforms.
INCLUDE(GNUInstallDirs)

# Build the Date-Time Range ManaGeR:
ADD_SUBDIRECTORY(dtrmgr)

# The accounting dump utility:
ADD_SUBDIRECTORY(slurm-acct-dump)

# The container pieces:
ADD_SUBDIRECTORY(container)

