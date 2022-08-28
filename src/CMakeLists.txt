
set(COMMON_SRCS util.c arch.c release.c)
set(COMMON_DIS_SRCS dis_woodstock.c dis_nut.c)
set(KML_SRCS kmll.l kmly.y kml.c)
set(STATE_IO_SRCS state_write.c state_read.c)
set(NONPAREIL_SRCS
    about.c
    calc_chooser.c
    coconut_lcd.c
    csim.c
    digit_ops.c
    display.c
    glib_async_queue_source.c
    goose.c
    helios.c
    helios_chargen.c
    keyboard.c
    mod1_file.c
    model.c
    phineas.c
    pixbuf_util.c
    platform.c
    printer.c
    proc.c
    proc_classic.c
    proc_nut.c
    proc_woodstock.c
    slide_switch.c
    sound.c
)

#----------------------------------------------------------------------
# kml lex/yacc
#----------------------------------------------------------------------

flex_target(kmllex kmll.l ${CMAKE_CURRENT_BINARY_DIR}/kmll.c)
bison_target(kmlparse kmly.y ${CMAKE_CURRENT_BINARY_DIR}/kmly.c
  DEFINES_FILE ${CMAKE_CURRENT_BINARY_DIR}/kmly.h
)
add_flex_bison_dependency(kmllex kmlparse)

#----------------------------------------------------------------------
# str2png
#----------------------------------------------------------------------

add_executable(str2png
  str2png.c ${COMMON_SRCS} ${KML_SRCS}
  ${BISON_kmlparse_OUTPUTS}
  ${FLEX_kmllex_OUTPUTS}
)
target_include_directories(str2png PRIVATE ${GTK2_INCLUDE_DIRS})
target_include_directories(str2png PRIVATE ${CMAKE_CURRENT_SOURCE_DIR})
target_include_directories(str2png PRIVATE ${CMAKE_CURRENT_BINARY_DIR})
target_link_libraries(str2png PRIVATE ${GTK2_LIBRARIES})

#----------------------------------------------------------------------
# bin2c
#----------------------------------------------------------------------

add_executable(bin2c bin2c.c ${COMMON_SRCS})
target_include_directories(bin2c PRIVATE ${GTK2_INCLUDE_DIRS})
target_link_libraries(bin2c PRIVATE ${GTK2_LIBRARIES})

#----------------------------------------------------------------------
# uasm
#----------------------------------------------------------------------

foreach (t asm wasm casm)
  flex_target(${t}lex ${t}l.l ${CMAKE_CURRENT_BINARY_DIR}/${t}l.c)
  bison_target(${t}parse ${t}y.y ${CMAKE_CURRENT_BINARY_DIR}/${t}y.c
    DEFINES_FILE ${CMAKE_CURRENT_BINARY_DIR}/${t}y.h
  )
  add_flex_bison_dependency(${t}lex ${t}parse)
endforeach()

add_executable(uasm "")
target_include_directories(uasm PRIVATE ${CMAKE_CURRENT_SOURCE_DIR})
target_sources(uasm
  PRIVATE
    ${COMMON_SRCS}
    asm.c
    symtab.c
    ${BISON_asmparse_OUTPUTS}
    ${FLEX_asmlex_OUTPUTS}
    ${BISON_casmparse_OUTPUTS}
    ${FLEX_casmlex_OUTPUTS}
    ${BISON_wasmparse_OUTPUTS}
    ${FLEX_wasmlex_OUTPUTS}
)

#----------------------------------------------------------------------
# modutil
#----------------------------------------------------------------------

add_executable(nonpareil_modutil "")
target_include_directories(nonpareil_modutil PRIVATE ${CMAKE_CURRENT_SOURCE_DIR})
target_sources(nonpareil_modutil
  PRIVATE
    ${COMMON_SRCS}
    modutil.c
)

#----------------------------------------------------------------------
# nonpareil image/sound preprocessing
#----------------------------------------------------------------------

function(gen_image name text str2png_args)
  string(REPLACE " " ";" str2png_args_list ${str2png_args})
  add_custom_command(
    OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/${name}_png.c
    COMMAND cmake -E copy ${CMAKE_SOURCE_DIR}/image/41cv.png ${CMAKE_CURRENT_BINARY_DIR}/41cv.png
    COMMAND ${CMAKE_CURRENT_BINARY_DIR}/str2png ${str2png_args_list} ${CMAKE_SOURCE_DIR}/kml/41cv.kml "${text}" ${CMAKE_CURRENT_BINARY_DIR}/${name}.png
    COMMAND ${CMAKE_CURRENT_BINARY_DIR}/bin2c ${CMAKE_CURRENT_BINARY_DIR}/${name}.png ${name}_png -c ${name}_png.c -h ${name}_png.h
    DEPENDS str2png bin2c ${CMAKE_SOURCE_DIR}/kml/41cv.kml ${CMAKE_SOURCE_DIR}/image/41cv.png
  )
endfunction()
gen_image(nonpareil_title "NONPAREIL" "-x 40 -y 44 -m 10")
gen_image(rgoose "." "-x 40 -y 44")
gen_image(lgoose "," "-x 40 -y 44")

function(gen_wave name)
  add_custom_command(
    OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/${name}_wav.c
    COMMAND cmake -E copy ${CMAKE_SOURCE_DIR}/sound/${name}.wav ${CMAKE_CURRENT_BINARY_DIR}/${name}.wav
    COMMAND ${CMAKE_CURRENT_BINARY_DIR}/bin2c ${CMAKE_SOURCE_DIR}/sound/${name}.wav ${name}_wav -c ${name}_wav.c -h ${name}_wav.h
    DEPENDS bin2c ${CMAKE_SOURCE_DIR}/sound/${name}.wav
  )
endfunction()
gen_wave(canada_goose)

#----------------------------------------------------------------------
# nonpareil
#----------------------------------------------------------------------

add_executable(nonpareil "")

if (ENABLE_DEBUGGER_GUI)
  set(NONPAREIL_DEBUGGER_GUI_SRCS debugger_gui.c)
  target_compile_definitions(nonpareil PRIVATE HAS_DEBUGGER=1)
  target_compile_definitions(nonpareil PRIVATE HAS_DEBUGGER_GUI=1)
endif()

if (ENABLE_DEBUGGER_CLI)
  set(NONPAREIL_DEBUGGER_CLI_SRCS debugger_cli.c)
  target_compile_definitions(nonpareil PRIVATE HAS_DEBUGGER=1)
  target_compile_definitions(nonpareil PRIVATE HAS_DEBUGGER_CLI=1)
  if (ENABLE_TCL)
    target_compile_definitions(nonpareil PRIVATE USE_TCL=1)
  endif()
  if (ENABLE_READLINE)
    target_compile_definitions(nonpareil PRIVATE USE_READLINE=1)
  endif()
endif()

target_compile_options(nonpareil PRIVATE -Wall)
target_compile_options(nonpareil PRIVATE -Werror)
target_compile_options(nonpareil PRIVATE -Wno-unused-function)
target_compile_options(nonpareil PRIVATE -Wno-deprecated-declarations)
target_compile_definitions(nonpareil PRIVATE DEFAULT_PATH=${CMAKE_INSTALL_FULL_DATADIR}/nonpareil)
target_include_directories(nonpareil PRIVATE
  ${CMAKE_CURRENT_BINARY_DIR}
  ${CMAKE_CURRENT_SOURCE_DIR}
  ${GTK2_INCLUDE_DIRS}
  ${LIBXML2_INCLUDE_DIRS}
  ${PNG_INCLUDE_DIRS}
  ${SDL_INCLUDE_DIR}
  ${SDL_INCLUDE_DIRS}
)
target_link_directories(nonpareil PRIVATE
  ${GTK2_LINK_LIBRARIES}
)
target_link_libraries(nonpareil PRIVATE
  ${FLEX_LIBRARIES}
  ${GTK2_LIBRARIES}
  ${LIBXML2_LIBRARIES}
  ${PNG_LIBRARIES}
  ${SDL_LIBRARY}
  ${SDL_LIBRARIES}
)
target_sources(nonpareil
  PRIVATE
    ${CMAKE_CURRENT_BINARY_DIR}/canada_goose_wav.c
    ${CMAKE_CURRENT_BINARY_DIR}/nonpareil_title_png.c
    ${CMAKE_CURRENT_BINARY_DIR}/lgoose_png.c
    ${CMAKE_CURRENT_BINARY_DIR}/rgoose_png.c
    ${BISON_kmlparse_OUTPUTS}
    ${FLEX_kmllex_OUTPUTS}
    ${COMMON_SRCS}
    ${COMMON_DIS_SRCS}
    ${KML_SRCS}
    ${STATE_IO_SRCS}
    ${NONPAREIL_SRCS}
    ${NONPAREIL_DEBUGGER_GUI_SRCS}
    ${NONPAREIL_DEBUGGER_CLI_SRCS}
)

#----------------------------------------------------------------------
# install
#----------------------------------------------------------------------

install(TARGETS nonpareil nonpareil_modutil uasm RUNTIME)
install(FILES ChangeLog DESTINATION ${CMAKE_INSTALL_DOCDIR})
