# **************************************************************************
# * Copyright(c) 1998-2014, ALICE Experiment at CERN, All rights reserved. *
# *                                                                        *
# * Author: The ALICE Off-line Project.                                    *
# * Contributors are mentioned in the code where appropriate.              *
# *                                                                        *
# * Permission to use, copy, modify and distribute this software and its   *
# * documentation strictly for non-commercial purposes is hereby granted   *
# * without fee, provided that the above copyright notice appears in all   *
# * copies and that both the copyright notice and this permission notice   *
# * appear in the supporting documentation. The authors make no claims     *
# * about the suitability of this software for any purpose. It is          *
# * provided "as is" without express or implied warranty.                  *
# **************************************************************************

# Module
set(MODULE TenderSupplies)
add_definitions(-D_MODULE_="${MODULE}")

# Module include folder
include_directories(${AliPhysics_SOURCE_DIR}/TENDER/${MODULE})

# Additional include folders in alphabetical order except ROOT
include_directories(${ROOT_INCLUDE_DIRS}
                    ${AliPhysics_SOURCE_DIR}/TENDER/Tender
                    ${AliPhysics_SOURCE_DIR}/CORRFW
                    ${AliPhysics_SOURCE_DIR}/OADB
                    ${AliPhysics_SOURCE_DIR}/PWG/EMCAL/EMCALbase
                   )

# Sources
set(SRCS
    AliAnalysisTaskVZEROEqFactorTask.cxx
    AliEMCALTenderSupply.cxx
    AliHMPIDTenderSupply.cxx
    AliPHOSTenderSupply.cxx
    AliPIDTenderSupply.cxx
    AliT0TenderSupply.cxx
    AliTOFTenderSupply.cxx
    AliTPCTenderSupply.cxx
    AliTrackFixTenderSupply.cxx
    AliTRDTenderSupply.cxx
    AliVtxTenderSupply.cxx
    AliVZEROTenderSupply.cxx
  )

# Headers from sources
string(REPLACE ".cxx" ".h" HDRS "${SRCS}")

# Generate the dictionary
# It will create G_ARG1.cxx and G_ARG1.h / ARG1 = function first argument
get_directory_property(incdirs INCLUDE_DIRECTORIES)
generate_dictionary("${MODULE}" "${MODULE}LinkDef.h" "${HDRS}" "${incdirs}")

# Generate the ROOT map
# Dependecies
set(LIBDEPS ANALYSIS ANALYSISalice Tender EMCALbase EMCALrec EMCALUtils PWGEMCALbase CORRFW OADB PHOSbase PHOSrec PHOSUtils AOD CDB ESD STEER STEERBase T0rec TOFbase TOFrec TPCbase TRDbase VZERObase VZEROrec Core Geom Gpad Graf Hist MathCore Matrix Physics RIO Tree)
generate_rootmap("${MODULE}" "${LIBDEPS}" "${CMAKE_CURRENT_SOURCE_DIR}/${MODULE}LinkDef.h")

# Add a library to the project using the specified source files
add_library_tested(${MODULE} SHARED ${SRCS} G__${MODULE}.cxx)
target_link_libraries(${MODULE} ANALYSIS ANALYSISalice Tender EMCALbase EMCALrec EMCALUtils PWGEMCALbase CORRFW OADB PHOSbase PHOSrec PHOSUtils AOD CDB ESD STEER STEERBase T0rec TOFbase TOFrec TPCbase TRDbase VZERObase VZEROrec Core Geom Gpad Graf Hist MathCore Matrix Physics RIO Tree)

# Additional compilation flags
set_target_properties(${MODULE} PROPERTIES COMPILE_FLAGS "")

# Linking the library
target_link_libraries(${MODULE} ${LIBDEPS})

# System dependent: Modify the way the library is build
if(${CMAKE_SYSTEM} MATCHES Darwin)
    set_target_properties(${MODULE} PROPERTIES LINK_FLAGS "-undefined dynamic_lookup")
endif(${CMAKE_SYSTEM} MATCHES Darwin)

# Installation
install(TARGETS ${MODULE}
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib)
install(FILES ${HDRS} DESTINATION include)

# Install macros
install(FILES AddTaskTender.C DESTINATION TENDER/TenderSupplies)

