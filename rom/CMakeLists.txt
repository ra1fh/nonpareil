
set(rom_list
  21.obj
  25.obj
  32e.obj
  33c.obj
  34c.obj
  37e.obj
  38c.obj
  38e.obj
  41cv.mod
  41cx.mod
  82143a.mod
  82182a.mod
)

foreach(rom ${rom_list})
  install(
    FILES ${nonpareil_roms_SOURCE_DIR}/rom/${rom}
    DESTINATION ${CMAKE_INSTALL_DATADIR}/nonpareil
  )
endforeach()
