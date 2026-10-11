# ---------------------------------------------------------------------------------------------
# install
# ---------------------------------------------------------------------------------------------

if (BUILD_ADSK_PLUGIN)
    if (IS_WINDOWS)
        configure_file("mayaUSD_Win.mod.template" ${PROJECT_BINARY_DIR}/mayaUSD.mod)
    else()
        configure_file("mayaUSD.mod.template" ${PROJECT_BINARY_DIR}/mayaUSD.mod)
    endif()
    install(FILES ${PROJECT_BINARY_DIR}/mayaUSD.mod DESTINATION ${CMAKE_INSTALL_PREFIX})
endif()

# Builds against a USD other than the one Autodesk's MayaUSD uses (see the template for why).
option(MAYAUSD_SHADOW_MAYAHYDRA "Install an empty MAYAHYDRA module that hides Autodesk's installed one" OFF)
if (MAYAUSD_SHADOW_MAYAHYDRA)
    configure_file("mayahydra_shadow.mod.template" ${PROJECT_BINARY_DIR}/mayahydra_shadow.mod)
    install(FILES ${PROJECT_BINARY_DIR}/mayahydra_shadow.mod DESTINATION ${CMAKE_INSTALL_PREFIX})
endif()

if (BUILD_HDMAYA)
    configure_file("mtoh.mod.template" ${PROJECT_BINARY_DIR}/mtoh.mod)
    install(FILES ${PROJECT_BINARY_DIR}/mtoh.mod DESTINATION ${CMAKE_INSTALL_PREFIX})
endif()

if (BUILD_PXR_PLUGIN)
    if (IS_WINDOWS)
        configure_file("pxrUSD_Win.mod.template" ${PROJECT_BINARY_DIR}/pxrUSD.mod)
    else()
        configure_file("pxrUSD.mod.template" ${PROJECT_BINARY_DIR}/pxrUSD.mod)
    endif()
    install(FILES ${PROJECT_BINARY_DIR}/pxrUSD.mod DESTINATION ${CMAKE_INSTALL_PREFIX})
endif()

if (BUILD_AL_PLUGIN)
    if (IS_WINDOWS)
        configure_file("alUSD_Win.mod.template" ${PROJECT_BINARY_DIR}/alUSD.mod)
    else()
        configure_file("alUSD.mod.template" ${PROJECT_BINARY_DIR}/alUSD.mod)
    endif()
    install(FILES ${PROJECT_BINARY_DIR}/alUSD.mod DESTINATION ${CMAKE_INSTALL_PREFIX})
endif()
