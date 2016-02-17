add_executable(tests-cbor tests-cbor.c)
target_link_libraries(tests-cbor cbor embUnit embUnit_textui)
set_target_properties(tests-cbor PROPERTIES COMPILE_DEFINITIONS "OUTPUT=OUTPUT_XML")
