cmake_minimum_required( VERSION 2.6 )
find_package( brainvisa-cmake REQUIRED )
BRAINVISA_PROJECT()

BRAINVISA_FIND_PACKAGE(documentation )
    
# Make sure Python can be executed
if( NOT DEFINED PYTHON_EXECUTABLE )
  find_package( PythonInterp REQUIRED )
endif( NOT DEFINED PYTHON_EXECUTABLE )
      
# Index of commands documentation
# if (NOT BRAINVISA_ADVANCED_FEATURE_TEST_MODE)
#   add_custom_target( bv_commands_doc 
#     COMMAND "${CMAKE_BINARY_DIR}/bin/bv_env${CMAKE_EXECUTABLE_SUFFIX}" "${PYTHON_HOST_EXECUTABLE}" ${CREATE_COMMANDS_DOC} -p "${CMAKE_BINARY_DIR}/bin" index_commands.html
#     WORKING_DIRECTORY ${CMAKE_BINARY_DIR}/share/doc
#     )
#     
#   add_custom_target( install-bv_commands_doc 
#     COMMAND "${CMAKE_BINARY_DIR}/bin/bv_env${CMAKE_EXECUTABLE_SUFFIX}" "${PYTHON_HOST_EXECUTABLE}" ${CREATE_COMMANDS_DOC} -p "$(BRAINVISA_INSTALL_PREFIX)/bin" "index_commands.html"
#     WORKING_DIRECTORY $(BRAINVISA_INSTALL_PREFIX)/share/doc
#     )
# endif()

# Documentation of axon processes and ontologies
add_custom_target( bv_ontology_doc
  COMMAND "${CMAKE_BINARY_DIR}/bin/bv_env${CMAKE_EXECUTABLE_SUFFIX}" "${PYTHON_EXECUTABLE}" ${CREATE_ONTOLOGY_DOC} ${CMAKE_BINARY_DIR}
  )

add_custom_target( install-bv_ontology_doc
  COMMAND "${CMAKE_BINARY_DIR}/bin/bv_env${CMAKE_EXECUTABLE_SUFFIX}" "${PYTHON_EXECUTABLE}" ${CREATE_ONTOLOGY_DOC} $(BRAINVISA_INSTALL_PREFIX)
  )

# Index of all documentation
add_custom_target( bv_doc_index 
  COMMAND "${CMAKE_BINARY_DIR}/bin/bv_env${CMAKE_EXECUTABLE_SUFFIX}" "${PYTHON_EXECUTABLE}" ${CREATE_DOC_INDEX} ${CMAKE_BINARY_DIR}/share/doc
  )

add_custom_target( install-bv_doc_index
  COMMAND "${CMAKE_BINARY_DIR}/bin/bv_env${CMAKE_EXECUTABLE_SUFFIX}" "${PYTHON_EXECUTABLE}" ${CREATE_DOC_INDEX} $(BRAINVISA_INSTALL_PREFIX)/share/doc
  )

add_dependencies( bv_doc_index bv_commands_doc bv_ontology_doc )
add_dependencies( doc bv_doc_index)
add_dependencies( bv_doc_index usrdoc devdoc)
add_dependencies( install-bv_doc_index install-bv_commands_doc install-bv_ontology_doc)
add_dependencies( install-${PROJECT_NAME} install-bv_doc_index )


# installer setup
# bv_create_installer create independent directories for each component, thus building a global doc is not possible on "installed" components.
# For now we have to build docs in the build diorectory, then copy the files in the corresponding packages.
# WARNING: this is not a clean solution, because:
# - obsolete files might remain in the build tree and get packaged
# - components which will not get in the packages will still have their docs packaged.
# The only better solution I see in the current framework is to install temporarily all components before running brainvisa-release, but it has a large overhead (all files will be copied twice).

# this one is the sale as bv_doc_index but without the heavy dependency on usrdoc and devdoc - we assume it has been already done.
add_custom_target( brainvisa-release_installer_doc
  COMMAND "${CMAKE_BINARY_DIR}/bin/bv_env_host${CMAKE_HOST_EXECUTABLE_SUFFIX}" "${PYTHON_HOST_EXECUTABLE}" ${CREATE_DOC_INDEX} ${CMAKE_BINARY_DIR}/share/doc
  )

# add_dependencies( brainvisa-release_installer_doc bv_commands_doc bv_ontology_doc )

add_custom_target( install-brainvisa-release_installer_doc
  COMMAND "${CMAKE_COMMAND}" -E copy ${CMAKE_BINARY_DIR}/share/doc/index_commands.html $(BRAINVISA_INSTALL_PREFIX)/share/doc/index_commands.html
  COMMAND "${CMAKE_COMMAND}" -E copy ${CMAKE_BINARY_DIR}/share/doc/doc_index.html $(BRAINVISA_INSTALL_PREFIX)/share/doc/doc_index.html
  )

add_dependencies( install-brainvisa-release_installer_doc brainvisa-release_installer_doc )

BRAINVISA_FIND_PACKAGE( axon QUIET )
if( AXON_FOUND )

  add_custom_target( install-brainvisa-release_installer_axon_doc
    COMMAND "${CMAKE_COMMAND}" -E copy_directory ${CMAKE_BINARY_DIR}/share/doc/axon-${axon_VERSION_MAJOR}.${axon_VERSION_MINOR}/ontology-all $(BRAINVISA_INSTALL_PREFIX)/share/doc/axon-${axon_VERSION_MAJOR}.${axon_VERSION_MINOR}/ontology-all
    COMMAND "${CMAKE_COMMAND}" -E copy_directory ${CMAKE_BINARY_DIR}/share/doc/axon-${axon_VERSION_MAJOR}.${axon_VERSION_MINOR}/en/processes $(BRAINVISA_INSTALL_PREFIX)/share/doc/axon-${axon_VERSION_MAJOR}.${axon_VERSION_MINOR}/en/processes
    COMMAND "${CMAKE_COMMAND}" -E copy_directory ${CMAKE_BINARY_DIR}/share/doc/axon-${axon_VERSION_MAJOR}.${axon_VERSION_MINOR}/fr/processes $(BRAINVISA_INSTALL_PREFIX)/share/doc/axon-${axon_VERSION_MAJOR}.${axon_VERSION_MINOR}/fr/processes
    )

  add_dependencies( install-brainvisa-release_installer_doc install-brainvisa-release_installer_axon_doc )

endif()

BRAINVISA_CREATE_CMAKE_CONFIG_FILES()
