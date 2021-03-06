find_package(Bullet QUIET)
if (NOT BULLET_FOUND)
  return()
endif()

include_directories(SYSTEM ${BULLET_INCLUDE_DIRS})

#--------------------------------------------------------
# Library Creation
#--------------------------------------------------------
SET (LIBRARY_NAME BulletCollision_plugin)
SET (LIB_MAJOR 0)
SET (LIB_MINOR 0)
SET (LIB_RELEASE 1)

file(GLOB SRCS *.cpp)

ADD_LIBRARY(${LIBRARY_NAME} SHARED 
  ${SRCS}
  )


if(VTK_LIBRARIES)
  TARGET_LINK_LIBRARIES(${LIBRARY_NAME}  
      scrimmage-core
    scrimmage-msgs
    RayTrace_plugin
    ${BULLET_LIBRARIES}
    ${VTK_LIBRARIES}
    )
else()
  TARGET_LINK_LIBRARIES(${LIBRARY_NAME}  
      scrimmage-core
    scrimmage-msgs
    RayTrace_plugin
    ${BULLET_LIBRARIES}
    )
endif()

SET (_soversion ${LIB_MAJOR}.${LIB_MINOR}.${LIB_RELEASE})

set_target_properties(${LIBRARY_NAME} PROPERTIES 
  SOVERSION ${LIB_MAJOR} 
  VERSION ${_soversion}
  LIBRARY_OUTPUT_DIRECTORY ${PROJECT_PLUGIN_LIBS_DIR}
  )

install(TARGETS ${LIBRARY_NAME}
  # IMPORTANT: Add the library to the "export-set"
  EXPORT ${PROJECT_NAME}-targets
  RUNTIME DESTINATION bin
  LIBRARY DESTINATION lib/${PROJECT_NAME}/plugin_libs
)

# Push up the PROJECT_PLUGINS variable
set(PROJECT_PLUGINS ${PROJECT_PLUGINS} ${LIBRARY_NAME} PARENT_SCOPE)
