list(APPEND CMAKE_MODULE_PATH 
	"${CMAKE_CURRENT_LIST_DIR}/cmake"
)

find_package(LATEX REQUIRED)
find_package(LatexMk REQUIRED)
find_package(TexLogFilter)

set(LRT_CHAPTERS_DIR  "${CMAKE_CURRENT_SOURCE_DIR}/chapters")
set(LRT_RESOURCES_DIR "${CMAKE_CURRENT_SOURCE_DIR}/resources")
set(LRT_DOCUMENT "${CMAKE_CURRENT_SOURCE_DIR}/document.tex")

configure_file("${CMAKE_CURRENT_SOURCE_DIR}/latexmkrc.in"
	"${CMAKE_CURRENT_BINARY_DIR}/latexmkrc"
	@ONLY
)

configure_file("${CMAKE_CURRENT_SOURCE_DIR}/cmake.tex.in"
	"cmake.tex"
	@ONLY
)

set(LATEXMK_OPTIONS 
	"-verbose"
)
set(TEXLOGFILTER_OPTIONS 
	"--ref --filename"
)

set(LOG_COMMENT "Trimming logs")
if(NOT TEXLOGFILTER_FOUND)
	add_custom_target(report_log
		COMMAND ${CMAKE_COMMAND} -E echo 
			"texlogfilter were not found."
		COMMENT ${LOG_COMMENT}
	)
else()
	add_custom_target(report_log
		COMMAND ${TEXLOGFILTER_EXECUTABLE}
			${CMAKE_CURRENT_BINARY_DIR}/document.log 
			${TEXLOGFILTER_OPTIONS}
		COMMENT ${LOG_COMMENT}
	)
	set(SILENT_OPTIONS "-silent")
endif()

add_custom_target(report_pdf
	COMMAND ${LATEXMK_EXECUTABLE} ${LRT_DOCUMENT}
		-r "${CMAKE_CURRENT_BINARY_DIR}/latexmkrc"
		"${SILENT_OPTIONS}" "${LATEXMK_OPTIONS}"
	WORKING_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}"
	BYPRODUCTS 
		"${CMAKE_CURRENT_BINARY_DIR}/out" 
		"${CMAKE_CURRENT_BINARY_DIR}/aux"
	COMMENT "Building pdf with latexmk"
)

if(TEXLOGFILTER_FOUND)
	configure_file("${CMAKE_CURRENT_SOURCE_DIR}/continuous.sh.in"
		"continious"
		FILE_PERMISSIONS OWNER_READ OWNER_WRITE OWNER_EXECUTE
		@ONLY
	)
endif()

add_custom_target(report_pdf_clean 
	COMMAND ${LATEXMK_EXECUTABLE} -c document.tex
	-r ${CMAKE_CURRENT_BINARY_DIR}/latexmkrc
	WORKING_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}"
	COMMENT "Cleaning aluxary files"
)

file(CREATE_LINK 
	"${CMAKE_CURRENT_BINARY_DIR}"
	"${CMAKE_CURRENT_SOURCE_DIR}/build"
	SYMBOLIC
)
