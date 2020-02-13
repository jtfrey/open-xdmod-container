# Generated content:
CONFIGURE_FILE(slurm-acct-dump.py.in slurm-acct-dump.py)

# Install:
INSTALL(PROGRAMS ${CMAKE_CURRENT_BINARY_DIR}/slurm-acct-dump.py DESTINATION ${CMAKE_INSTALL_SBINDIR})
