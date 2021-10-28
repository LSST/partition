find_package(Boost REQUIRED
    filesystem REQUIRED
    program_options REQUIRED
    thread REQUIRED
)

find_package(Threads REQUIRED)

#----------------------------------------------------------------

add_library(partition SHARED)

target_compile_features(partition PRIVATE
    cxx_std_14
)

set_target_properties(partition PROPERTIES
    CXX_EXTENSIONS OFF
    POSITION_INDEPENDENT_CODE ON
)

target_include_directories(partition PUBLIC
    ${PROJECT_SOURCE_DIR}/include
)

target_sources(partition PRIVATE
    Chunker.cc
    ChunkIndex.cc
    ChunkReducer.cc
    CmdLineUtils.cc
    ConfigStore.cc
    Csv.cc
    FileUtils.cc
    Geometry.cc
    HtmIndex.cc
    InputLines.cc
    ObjectIndex.cc
)

target_link_libraries(partition PUBLIC
    boost_program_options
    boost_thread
    Threads::Threads
)

install(TARGETS partition)

#----------------------------------------------------------------

FUNCTION(partition_apps)
    FOREACH(APP IN ITEMS ${ARGV})
        add_executable(${APP} ${APP}.cc)
        target_compile_features(${APP} PUBLIC cxx_std_14)    
        set_target_properties(${APP} PROPERTIES
            CXX_EXTENSIONS OFF
            POSITION_INDEPENDENT_CODE ON
        )
        target_link_libraries(${APP} PRIVATE
            partition
            boost_filesystem
            sphgeom
        )
        install(TARGETS ${APP})    
    ENDFOREACH()
ENDFUNCTION()

partition_apps(
    sph-duplicate
    sph-duplicate2
    sph-estimate-stats
    sph-htm-index
    sph-layout
    sph-partition-matches
    sph-partition    
)
