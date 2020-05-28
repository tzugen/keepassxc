#  Copyright (C) 2020 KeePassXC Team <team@keepassxc.org>
#
#  This program is free software: you can redistribute it and/or modify
#  it under the terms of the GNU General Public License as published by
#  the Free Software Foundation, either version 2 or (at your option)
#  version 3 of the License.
#
#  This program is distributed in the hope that it will be useful,
#  but WITHOUT ANY WARRANTY; without even the implied warranty of
#  MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
#  GNU General Public License for more details.
#
#  You should have received a copy of the GNU General Public License
#  along with this program.  If not, see <http://www.gnu.org/licenses/>.

find_program(ASCIIDOCTOR_EXE asciidoctor)
if(NOT ASCIIDOCTOR_EXE)
    message(FATAL_ERROR "asciidoctor is required to build documentation")
else()
    message(STATUS "Using asciidoctor: ${ASCIIDOCTOR_EXE}")
endif()

add_custom_command(OUTPUT KeePassXC_GettingStarted.html
    COMMAND ${ASCIIDOCTOR_EXE} -D ${CMAKE_CURRENT_BINARY_DIR} -o KeePassXC_GettingStarted.html ${CMAKE_CURRENT_SOURCE_DIR}/GettingStarted.adoc
    DEPENDS ./*
    VERBATIM
)
add_custom_command(OUTPUT KeePassXC_UserGuide.html
    COMMAND ${ASCIIDOCTOR_EXE} -D ${CMAKE_CURRENT_BINARY_DIR} -o KeePassXC_UserGuide.html ${CMAKE_CURRENT_SOURCE_DIR}/UserGuide.adoc
    DEPENDS ./*
    VERBATIM
)
add_custom_target(docs DEPENDS KeePassXC_GettingStarted.html KeePassXC_UserGuide.html)
install(FILES
        ${CMAKE_CURRENT_BINARY_DIR}/KeePassXC_GettingStarted.html
        ${CMAKE_CURRENT_BINARY_DIR}/KeePassXC_UserGuide.html
    DESTINATION ${DATA_INSTALL_DIR}/docs)

if(APPLE OR UNIX)
    execute_process(COMMAND ${ASCIIDOCTOR_EXE} -b manpage ${CMAKE_CURRENT_SOURCE_DIR}/man/keepassxc.1.adoc)
    install(FILES ${CMAKE_CURRENT_BINARY_DIR}/keepassxc.1 DESTINATION ${CMAKE_INSTALL_MANDIR}/man1/)

    execute_process(COMMAND ${ASCIIDOCTOR_EXE} -b manpage ${CMAKE_SOURCE_DIR}/share/docs/man/keepassxc-cli.1.adoc)
    install(FILES ${CMAKE_CURRENT_BINARY_DIR}/keepassxc-cli.1 DESTINATION ${CMAKE_INSTALL_MANDIR}/man1/)
endif()
