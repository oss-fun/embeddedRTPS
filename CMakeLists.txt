cmake_minimum_required(VERSION 3.5)

project(embeddedrtps)

find_package(ament_cmake_ros REQUIRED)
find_package(microcdr REQUIRED)

set(ERTPS_MAX_HISTORY "10" CACHE STRING 
"This value sets the number of history slots available for RMW subscriptions, requests and replies")

set(ERTPS_MAX_PARTICIPANTS "1" CACHE STRING "This value sets the maximum number of rtps participants.")

# TODO: fix difference between best effort and reliable writers/readers
set(ERTPS_MAX_PUBLISHERS "1" CACHE STRING "This value sets the maximum number of publishers for a domain.")
set(ERTPS_MAX_SUBSCRIPTIONS "1" CACHE STRING "This value sets the maximum number of subscriptions for a domain.")

set(ERTPS_MAX_SERVICES "1" CACHE STRING "This value sets the maximum number of services for a domain.")
set(ERTPS_MAX_CLIENTS "1" CACHE STRING "This value sets the maximum number of clients for a domain.")
set(ERTPS_TOPIC_NAME_MAX_LENGTH "200" CACHE STRING "This value sets the maximum number of characters for a topic name.")
set(ERTPS_TYPE_NAME_MAX_LENGTH "200" CACHE STRING "This value sets the maximum number of characters for a type name.")

set(ERTPS_PROXIES_PER_WRITER "10" CACHE STRING "This value sets the maximum number of matched readers per writer.")
set(ERTPS_PROXIES_PER_READER "10" CACHE STRING "This value sets the maximum number of matched writers per reader.")
set(ERTPS_MAX_UDP_CONNECTIONS "15" CACHE STRING "This value sets the maximum number of UDP connections.")

# Create source files with the define
configure_file(${PROJECT_SOURCE_DIR}/include/rtps/config.h.in
  ${PROJECT_SOURCE_DIR}/include/rtps/config.h)

set(${PROJECT_NAME}_sources
    src/discovery/TopicData.cpp
    src/discovery/ParticipantProxyData.cpp
    src/discovery/SEDPAgent.cpp
    src/discovery/SPDPAgent.cpp
    src/messages/MessageReceiver.cpp
    src/messages/MessageTypes.cpp
    src/ThreadPool.cpp
    src/communication/UdpDriver.cpp
    src/entities/Participant.cpp
    src/entities/Domain.cpp
    src/entities/StatelessReader.cpp
    src/storages/PBufWrapper.cpp
    src/storages/SimpleHistoryCache.cpp
    src/storages/HistoryCache.cpp
    src/rtps.cpp
    thirdparty/lwip/default_netif.c
)


add_library(${PROJECT_NAME} ${${PROJECT_NAME}_sources})

ament_target_dependencies(${PROJECT_NAME} microcdr)


target_include_directories(${PROJECT_NAME} PUBLIC
    "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>"
    "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/thirdparty/lwip>"
    "$<INSTALL_INTERFACE:include>")

target_include_directories(${PROJECT_NAME} PRIVATE
    "$<BUILD_INTERFACE:${microcdr_INCLUDE_DIR}>"
  )

ament_export_include_directories(include)
ament_export_libraries(${PROJECT_NAME})
ament_export_targets(${PROJECT_NAME})
ament_export_include_directories(${microcdr_INCLUDE_DIR})

ament_package()

# Install includes.
install(
  DIRECTORY include/
  DESTINATION include
)

# Install library.
install(
  TARGETS ${PROJECT_NAME} EXPORT ${PROJECT_NAME}
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION bin)