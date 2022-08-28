
set(KML_FILES 35 45 55 80 21 25 32e 33c 34c 37e 38c 38e 41cv 41cx)

foreach(kml ${KML_FILES})
  install(FILES ${kml}.kml DESTINATION ${CMAKE_INSTALL_DATADIR}/nonpareil)
  install(CODE "execute_process(COMMAND ${CMAKE_COMMAND} -E create_symlink nonpareil ${CMAKE_INSTALL_FULL_BINDIR}/${kml})")
endforeach()
