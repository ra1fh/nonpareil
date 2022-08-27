
set(asm_list 35 45 55 80)

foreach (asm ${asm_list})
  add_custom_command(
    OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/${asm}.obj ${CMAKE_CURRENT_BINARY_DIR}/${asm}.lst
    COMMAND $<TARGET_FILE:uasm> ${nonpareil_roms_SOURCE_DIR}/asm/${asm}.asm -o ${CMAKE_CURRENT_BINARY_DIR}/${asm}.obj -l ${CMAKE_CURRENT_BINARY_DIR}/${asm}.lst
    DEPENDS uasm ${nonpareil_roms_SOURCE_DIR}/asm/${asm}.asm
  )
  add_custom_target(rom${asm} ALL
    DEPENDS ${CMAKE_CURRENT_BINARY_DIR}/${asm}.obj
  )
  install(FILES ${nonpareil_roms_SOURCE_DIR}/asm/${asm}.asm DESTINATION ${CMAKE_INSTALL_DATADIR}/nonpareil)
  install(FILES ${CMAKE_CURRENT_BINARY_DIR}/${asm}.lst DESTINATION ${CMAKE_INSTALL_DATADIR}/nonpareil)
  install(FILES ${CMAKE_CURRENT_BINARY_DIR}/${asm}.obj DESTINATION ${CMAKE_INSTALL_DATADIR}/nonpareil)
endforeach()
