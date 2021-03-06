find_package(GTest REQUIRED)

set(PXR_PACKAGE AL_USDMayaTestPlugin)

list(APPEND AL_maya_test_headers
    AL/UnitTestHarness.h
    test_usdmaya.h
)

list(APPEND AL_maya_test_source
        plugin.cpp
		AL/UnitTestHarness.cpp
        AL/maya/test_DiffCore.cpp
        AL/maya/test_EventHandler.cpp
        AL/maya/test_MatrixToSRT.cpp
        AL/maya/test_MayaEventManager.cpp
        AL/usdmaya/commands/test_ExportCommands.cpp
        AL/usdmaya/commands/test_LayerCommands.cpp
        AL/usdmaya/commands/test_ProxyShapeSelect.cpp
        AL/usdmaya/commands/test_InternalProxySelection.cpp
        AL/usdmaya/commands/test_ProxyShapeImport.cpp
        AL/usdmaya/commands/test_LayerManagerCommands.cpp
        AL/usdmaya/fileio/export_blendshape.cpp
        AL/usdmaya/fileio/export_constraints.cpp
        AL/usdmaya/fileio/export_ik.cpp
        AL/usdmaya/fileio/export_import_instancing.cpp
        AL/usdmaya/fileio/export_lattice.cpp
        AL/usdmaya/fileio/export_nonlinear.cpp
        AL/usdmaya/fileio/export_unmerged.cpp
        AL/usdmaya/fileio/export_multiple_shapes.cpp
        AL/usdmaya/nodes/test_ActiveInactive.cpp
        AL/usdmaya/nodes/test_LayerManager.cpp
        AL/usdmaya/nodes/test_ProxyShape.cpp
        AL/usdmaya/nodes/test_Transform.cpp
        AL/usdmaya/nodes/test_TransformMatrix.cpp
        AL/usdmaya/nodes/test_TranslatorContext.cpp
        AL/usdmaya/nodes/test_ProxyShapeSelectabilityDB.cpp
        AL/usdmaya/nodes/proxy/test_DrivenTransforms.cpp
        AL/usdmaya/nodes/proxy/test_PrimFilter.cpp
        AL/usdmaya/test_SelectabilityDB.cpp
        AL/usdmaya/test_DiffPrimVar.cpp
        AL/usdmaya/commands/test_TranslateCommand.cpp
        test_translators_AnimationTranslator.cpp
        test_translators_CameraTranslator.cpp
        test_translators_DgTranslator.cpp
        test_translators_MeshTranslator.cpp
        test_translators_NurbsCurveTranslator.cpp
        test_translators_TransformTranslator.cpp
        test_translators_Translator.cpp
        test_usdmaya_AttributeType.cpp
        test_usdmaya.cpp
)

add_library(${PXR_PACKAGE} ${AL_maya_test_source})

target_compile_definitions(${PXR_PACKAGE}
PRIVATE
    AL_USDMAYA_UNITTEST
    AL_USDMAYATEST_PLUGIN_EXPORT
    AL_USDMAYA_TEST_DATA="${CMAKE_CURRENT_SOURCE_DIR}/test_data"
)

target_link_libraries(${PXR_PACKAGE}
    AL_EventSystem
    AL_USDMayaUtils
    AL_MayaUtils
    AL_USDUtils
    AL_USDMaya
    ${GTEST_LIBRARIES}
    ${MAYA_LIBRARIES}
)

target_include_directories(${PXR_PACKAGE}
PRIVATE
    ${GTEST_INCLUDE_DIRS}
    ${MAYA_INCLUDE_DIRS}
    "../../lib/AL_USDMaya"    
)

if(MSVC)
    set_target_properties(${PXR_PACKAGE} PROPERTIES SUFFIX ".mll")
else()
    set(rpath "$ORIGIN:$ORIGIN/../lib")
    if (CMAKE_INSTALL_RPATH)
        set(rpath "${CMAKE_INSTALL_RPATH}:${rpath}")
    endif()
    set_target_properties(${PXR_PACKAGE}
            PROPERTIES
            PREFIX ""
            INSTALL_RPATH "${rpath}"
    )
endif()

install(TARGETS
    ${PXR_PACKAGE}
    LIBRARY
        DESTINATION plugin
    RUNTIME
        DESTINATION plugin
)

add_test(
    NAME TestAL_USDMaya
    COMMAND
        ${CMAKE_CURRENT_SOURCE_DIR}/run_mayaplugin_tests.sh
            ${CMAKE_INSTALL_PREFIX}
            ${CMAKE_CURRENT_SOURCE_DIR}
)
