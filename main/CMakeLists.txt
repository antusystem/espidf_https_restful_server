idf_component_register(
    SRCS "esp_rest_main.c" "rest_server.c"
    INCLUDE_DIRS "."
    EMBED_TXTFILES "certs/cacert.pem" "certs/prvtkey.pem"
)
