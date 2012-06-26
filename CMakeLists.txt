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
#     COMMAND "${CMAKE_BINARY_DIR}/bin/bv_env" "${PYTHON_EXECUTABLE}" ${CREATE_COMMANDS_DOC} -p "${CMAKE_BINARY_DIR}/bin" index_commands.html
#     WORKING_DIRECTORY ${CMAKE_BINARY_DIR}/share/doc
#     )
#     
#   add_custom_target( install-bv_commands_doc 
#     COMMAND "${CMAKE_BINARY_DIR}/bin/bv_env" "${PYTHON_EXECUTABLE}" ${CREATE_COMMANDS_DOC} -p "$(BRAINVISA_INSTALL_PREFIX)/bin" "index_commands.html"
#     WORKING_DIRECTORY $(BRAINVISA_INSTALL_PREFIX)/share/doc
#     )
# endif()

# Documentation of axon processes and ontologies
add_custom_target( bv_ontology_doc
  COMMAND "${CMAKE_BINARY_DIR}/bin/bv_env" "${PYTHON_EXECUTABLE}" ${CREATE_ONTOLOGY_DOC} ${CMAKE_BINARY_DIR}
  )

add_custom_target( install-bv_ontology_doc
  COMMAND "${CMAKE_BINARY_DIR}/bin/bv_env" "${PYTHON_EXECUTABLE}" ${CREATE_ONTOLOGY_DOC} $(BRAINVISA_INSTALL_PREFIX)
  )

# Index of all documentation
add_custom_target( bv_doc_index 
  COMMAND "${CMAKE_BINARY_DIR}/bin/bv_env" "${PYTHON_EXECUTABLE}" ${CREATE_DOC_INDEX} ${CMAKE_BINARY_DIR}/share/doc
  )
  
add_custom_target( install-bv_doc_index
  COMMAND "${CMAKE_BINARY_DIR}/bin/bv_env" "${PYTHON_EXECUTABLE}" ${CREATE_DOC_INDEX} $(BRAINVISA_INSTALL_PREFIX)/share/doc
  )
  
add_dependencies( bv_doc_index bv_commands_doc bv_ontology_doc )
add_dependencies( doc bv_doc_index)
add_dependencies( bv_doc_index usrdoc devdoc)
add_dependencies( install-bv_doc_index install-bv_commands_doc install-bv_ontology_doc)
add_dependencies( install-${PROJECT_NAME} install-bv_doc_index )

#BRAINVISA_CREATE_CMAKE_CONFIG_FILES()


