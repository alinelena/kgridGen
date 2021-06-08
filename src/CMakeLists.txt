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

