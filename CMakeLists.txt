cmake_minimum_required (VERSION 2.6)


# ==================
# Define Our Project
# ==================

set (My_Project_Title "Some Project")
set (My_Project_Author "Somebody")
set (My_Project_Revised_Date "2015-06-05")
set (My_Project_Version_Major 1)
set (My_Project_Version_Minor 0)
set (My_Project_Version_Patch 0)

set (My_Project_Copyright_Date "2015")
set (My_Project_Copyright "Copyright © ${My_Project_Copyright_Date} ${My_Project_Author}.")

project (${My_Project_Title})


# Search for included files here
include_directories( ${PROJECT_SOURCE_DIR}/src )
include_directories( ${PROJECT_SOURCE_DIR}/test )


string(TOUPPER ${My_Project_Title} My_Project_Title_Caps  )
string(REGEX REPLACE " " "_" My_Project_Title_Caps ${My_Project_Title_Caps} )


# ======================
# Process Template Files
# ======================

file(READ ${PROJECT_SOURCE_DIR}/LICENSE.txt My_Project_License)

string(REGEX REPLACE "\n" "\n\t" My_Project_License_Indent ${My_Project_License})

configure_file (
	"${PROJECT_SOURCE_DIR}/templates/template.c.in"
	"${PROJECT_BINARY_DIR}/template.c"
)

configure_file (
	"${PROJECT_SOURCE_DIR}/templates/template.h.in"
	"${PROJECT_BINARY_DIR}/template.h"
)



# ===========================================
# Build Test Suite with CuTest (unit testing)
# ===========================================

set(test_files
	test/CuTest.c
	test/CuTest.h
	${PROJECT_BINARY_DIR}/AllTests.c
)

if (DEFINED TEST)
	add_definitions(-DTEST)

	add_executable(run_tests
		${test_files}
		${src_files}
		${header_files}
	)

	# Process source files to look for tests to run
	add_custom_command (
		OUTPUT ${PROJECT_BINARY_DIR}/AllTests.c
		COMMAND sh ${PROJECT_SOURCE_DIR}/test/make-tests.sh ${PROJECT_SOURCE_DIR}/src/*.c > ${PROJECT_BINARY_DIR}/AllTests.c
	)

	enable_testing()

	add_test( test ${PROJECT_BINARY_DIR}/run_tests)

endif()


# ==========================
# Build Installer with CPack
# ==========================

# You're on your own here
