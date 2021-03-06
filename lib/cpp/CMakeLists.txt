include_directories(${mosquitto_SOURCE_DIR}/lib ${mosquitto_SOURCE_DIR}/lib/cpp
			${STDBOOL_H_PATH} ${STDINT_H_PATH})
link_directories(${mosquitto_BINARY_DIR}/lib)

set(CPP_SRC mosquittopp.cpp mosquittopp.h)

add_library(mosquittopp SHARED ${CPP_SRC})
set_target_properties(mosquittopp PROPERTIES
	POSITION_INDEPENDENT_CODE 1
)
target_link_libraries(mosquittopp libmosquitto)
set_target_properties(mosquittopp PROPERTIES
	VERSION ${VERSION}
	SOVERSION 1
)
install(TARGETS mosquittopp RUNTIME DESTINATION "${BINDIR}" LIBRARY DESTINATION "${LIBDIR}")

if (${WITH_STATIC_LIBRARIES} STREQUAL ON)
	add_library(mosquittopp_static STATIC
		${C_SRC}
		${CPP_SRC}
	)
	if (${WITH_PIC} STREQUAL ON)
		set_target_properties(mosquittopp_static PROPERTIES
			POSITION_INDEPENDENT_CODE 1
		)
	endif (${WITH_PIC} STREQUAL ON)

	target_link_libraries(mosquittopp_static ${LIBRARIES})

	set_target_properties(mosquittopp_static PROPERTIES
		OUTPUT_NAME mosquittopp
		VERSION ${VERSION}
	)

	target_compile_definitions(mosquittopp_static PUBLIC "LIBMOSQUITTO_STATIC")
	install(TARGETS mosquittopp_static RUNTIME DESTINATION "${BINDIR}" ARCHIVE DESTINATION "${LIBDIR}")
endif (${WITH_STATIC_LIBRARIES} STREQUAL ON)

install(FILES mosquittopp.h DESTINATION "${INCLUDEDIR}")

if (UNIX AND NOT APPLE)
	install(CODE "EXEC_PROGRAM(/sbin/ldconfig)")
endif (UNIX AND NOT APPLE)

