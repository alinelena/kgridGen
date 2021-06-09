add_library(kptgen
  kpointgeneration.f90
  wrap_kpoints.f90
  )

target_include_directories(kptgen PUBLIC  ${SYMLIB_INC})

add_executable(kptgen.x
  driver.f90
  )
target_link_libraries(kptgen.x PRIVATE kptgen ${LIBS})

INSTALL_SUBLIB(kptgen)
install(FILES ${PROJECT_SOURCE_DIR}/src/kpointgeneration.f90 DESTINATION ${CMAKE_INSTALL_PREFIX}/include)
install(FILES ${CMAKE_CURRENT_BINARY_DIR}/kpointgeneration.mod DESTINATION ${CMAKE_INSTALL_LIBDIR})

