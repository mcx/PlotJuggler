set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

qt5_add_resources(RES_SRC resource.qrc)

qt5_wrap_ui(
  UI_SRC
  aboutdialog.ui
  mainwindow.ui
  support_dialog.ui
  curvelist_panel.ui
  preferences_dialog.ui
  suggest_dialog.ui
  new_release_dialog.ui
  multifile_prefix.ui
  colormap_editor.ui
  colormap_selector.ui
  statistics_dialog.ui
  plot_docker_toolbar.ui
  plotwidget_editor.ui
  transforms/function_editor.ui
  transforms/function_editor_help.ui
  transforms/transform_selector.ui
  transforms/first_derivative.ui
  transforms/moving_average_filter.ui
  transforms/moving_variance.ui
  transforms/moving_rms.ui
  transforms/outlier_removal.ui
  transforms/integral_transform.ui
  transforms/scale_transform.ui
  transforms/samples_count.ui
  cheatsheet/cheatsheet_dialog.ui)

set(PLOtJUGGLER_SRC
    cheatsheet/cheatsheet_dialog.cpp
    customtracker.cpp
    multifile_prefix.cpp
    colormap_editor.cpp
    colormap_selector.cpp
    color_map.cpp
    curvelist_panel.cpp
    curvelist_view.cpp
    curvetree_view.cpp
    dummy_data.cpp
    main.cpp
    mainwindow.cpp
    messageparser_base.cpp
    menubar.cpp
    new_release_dialog.cpp
    # plotlegend.cpp
    plotwidget.cpp
    plotwidget_editor.cpp
    plotwidget_transforms.cpp
    plot_docker.cpp
    plot_docker_toolbar.cpp
    preferences_dialog.cpp
    point_series_xy.cpp
    # plotzoomer.cpp
    plot_background.cpp
    statistics_dialog.cpp
    suggest_dialog.cpp
    # timeseries_qwt.cpp
    tabbedplotwidget.cpp
    tab_widget.h
    tree_completer.h
    transforms/custom_function.cpp
    transforms/function_editor.cpp
    transforms/transform_selector.cpp
    transforms/lua_custom_function.cpp
    transforms/moving_average_filter.cpp
    transforms/moving_rms.cpp
    transforms/moving_variance.cpp
    transforms/outlier_removal.cpp
    transforms/integral_transform.cpp
    transforms/absolute_transform.cpp
    transforms/samples_count.cpp
    transforms/first_derivative.cpp
    transforms/scale_transform.cpp
    utils.h
    utils.cpp
    mainwindow.h
    realslider.h
    nlohmann_parsers.cpp)

add_executable(plotjuggler ${PLOtJUGGLER_SRC} ${RES_SRC} ${UI_SRC}
                           ${BACKWARD_ENABLE})

if(NOT WIN32)
  add_backward(plotjuggler)
endif()

if(APPIMAGE_BUILD)
  message(STATUS "Building PlotJuggler as an AppImage")
  target_compile_definitions(plotjuggler PRIVATE APPIMAGE_BUILD)
endif()

target_include_directories(
  plotjuggler
  PRIVATE
    $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/3rdparty/color_widgets/include>
    $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/3rdparty/Qt-Advanced-Docking/include>
    $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/3rdparty/QCodeEditor/include>)

target_link_libraries(
  plotjuggler
  PRIVATE ${QT_LINK_LIBRARIES}
          plotjuggler_base
          colorwidgets
          qt_advanced_docking
          plotjuggler_qwt
          QCodeEditor
          nlohmann_json::nlohmann_json
          fmt::fmt
          lua::lua)

if(COMPILING_WITH_CATKIN)
  target_link_libraries(plotjuggler PRIVATE ${catkin_LIBRARIES})
  install(
    TARGETS plotjuggler
    RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
    LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
    ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION})
elseif(COMPILING_WITH_AMENT)

  target_link_libraries(plotjuggler PRIVATE ament_index_cpp::ament_index_cpp
                                            rclcpp::rclcpp)

  install(TARGETS plotjuggler DESTINATION lib/${PROJECT_NAME})
else()
  install(TARGETS plotjuggler DESTINATION bin)
endif()
