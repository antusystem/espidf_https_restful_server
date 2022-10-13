# ESP-IDF HTTPS Restful API Server Example

## Overview

This example mainly introduces how to implement a RESTful API server and HTTPS server on ESP32, with a frontend browser UI.

This example is the union of [sd_card-sdmmc](https://github.com/espressif/esp-idf/tree/release/v4.4/examples/storage/sd_card/sdmmc), [wifi-station](https://github.com/espressif/esp-idf/tree/release/v4.4/examples/wifi/getting_started/station), [https_server-simple](https://github.com/espressif/esp-idf/tree/release/v4.4/examples/protocols/https_server/simple), and [http_server-restful_server](https://github.com/espressif/esp-idf/tree/release/v4.4/examples/protocols/http_server/restful_server). The certificate used is the one in the **https server** example. This documentition is basically a copy of the one in **http server - restful server**.

This example designs several APIs to fetch resources as follows:

| API                        | Method | Resource Example                                      | Description                                                                              | Page URL |
| -------------------------- | ------ | ----------------------------------------------------- | ---------------------------------------------------------------------------------------- | -------- |
| `/api/v1/system/info`      | `GET`  | {<br />version:"v4.0-dev",<br />cores:2<br />}        | Used for clients to get system information like IDF version, ESP32 cores, etc            | `/`      |
| `/api/v1/temp/raw`         | `GET`  | {<br />raw:22<br />}                                  | Used for clients to get raw temperature data read from sensor                            | `/chart` |
| `/api/v1/light/brightness` | `POST` | { <br />red:160,<br />green:160,<br />blue:160<br />} | Used for clients to upload control values to ESP32 in order to control LED’s brightness  | `/light` |

**Page URL** is the URL of the webpage which will send a request to the API.

### TO DO

* Check mDNS


### About mDNS

The IP address of an IoT device may vary from time to time, so it’s impracticable to hard code the IP address in the webpage. In this example, we use the `mDNS` to parse the domain name `esp-home.local`, so that we can alway get access to the web server by this URL no matter what the real IP address behind it. See [here](https://docs.espressif.com/projects/esp-idf/en/latest/api-reference/protocols/mdns.html) for more information about mDNS.

**Notes: mDNS is installed by default on most operating systems or is available as separate package.**

### About deploy mode

In development mode, it would be awful to flash the whole webpages every time we update the html, js or css files. This examples uses an SD Card to store the webpage elements. SD cards are an option when the website after built is very large that the SPI Flash have not enough space to hold (e.g. larger than 2MB).

### About frontend framework

This examples uses the frontend framework [Vue](https://vuejs.org/) as example and adopt the [vuetify](https://vuetifyjs.com/) as the UI library.

## How to use example

### Hardware Required

This example was tested in a ESP32 CAM Kit ([this one](https://www.aideepen.com/products/esp32-cam-wifi-bluetooth-module-with-ov2640-camera-module-development-board-esp32-support-ov2640-and-ov7670-cameras-5v?_pos=1&_sid=c20759f44&_ss=r)) which is an AI-Thinker devboard.

#### Pin Assignment:

To deploy the website in the SD Card you must have this pin connection for this example.

| ESP32  | SD Card |
| ------ | ------- |
| GPIO2  | D0      |
| GPIO4  | D1      |
| GPIO12 | D2      |
| GPIO13 | D3      |
| GPIO14 | CLK     |
| GPIO15 | CMD     |

If you have the ESP32 CAM from Ai-Thinker then the flash is also connected to GPI04, so the flash will turn on when reading the SD Card.

### Configure the project

Open the project configuration menu (`idf.py menuconfig`). 

In the `Example Configuration` menu:

* Set the domain name in `mDNS Host Name` option.
* Set the mount point of the website in `Website mount point in VFS` option, the default value is `/www`.
* You can configure the Wifi connection here or in `Example Connection Configuration`, you must indicate the `SSID` and `password`, the `maximun retry`, and the `WiFi Scan auth mode threshold`. You can also select the `WiFi Scan Method`.

### Build and Flash

After the webpage design work has been finished, you should compile them by running following commands:

```bash
cd path_to_this_example/front/web-demo
npm install
npm run build
```

After a while, you will see a `dist` directory which contains all the website files (e.g. html, js, css, images), the content of this folder must be copied to the root of the SD Card.

Run `idf.py -p PORT flash monitor` to build and flash the project..

(To exit the serial monitor, type ``Ctrl-]``.)

See the [Getting Started Guide](https://docs.espressif.com/projects/esp-idf/en/latest/get-started/index.html) for full steps to configure and use ESP-IDF to build projects.

**Note:** I mainly used the ESP-IDF Extension for Visual Studio Code in the development to compile, flash, and monitor.

## Example Output

### Render webpage in browser

In your browser, enter the URL where the website located (e.g. `https://esp-home.local`). You can also enter the IP address that ESP32 obtained if your operating system currently don't have support for mDNS service.

Besides that, this example also enables the NetBIOS feature with the domain name `esp-home`. If your OS supports NetBIOS and has enabled it (e.g. Windows has native support for NetBIOS), then the URL `https://esp-home` should also work.

![esp_home_local](https://dl.espressif.com/dl/esp-idf/docs/_static/esp_home_local.gif)

### ESP monitor output

After you connect to the page (and maybe accept to enter it after a certificate warning) this is the log:

```
I (892) example: wifi_init_sta finished.
I (952) wifi:APs beacon interval = 102400 us, DTIM period = 1
I (2662) esp_netif_handlers: sta ip: XXX.XXX.XX, mask: XX.XX.XX, gw: XX.XX
I (2662) example: got ip: XX.XXX.XX
I (2662) example: connected to ap SSID:myssid password:mypass
I (2672) gpio: GPIO[13]| InputEn: 0| OutputEn: 1| OpenDrain: 0| Pullup: 0| Pulldown: 0| Intr:0 
I (2712) esp-rest: Starting HTTP Server
I (2712) esp_https_server: Starting server
I (2722) esp_https_server: Server listening on port 443
I (114982) esp_https_server: performing session handshake
I (117052) esp-rest: File sending complete
I (117332) esp-rest: File sending complete
I (117342) esp_https_server: performing session handshake
I (119412) esp_https_server: performing session handshake
I (122532) esp-rest: File sending complete
I (123112) esp-rest: File sending complete
I (123492) esp-rest: File sending complete
I (133582) esp_https_server: performing session handshake
I (135692) esp-rest: File sending complete
I (136322) esp-rest: File sending complete
I (136342) esp_https_server: performing session handshake
I (138402) esp-rest: File sending complete
I (138412) esp_https_server: performing session handshake
I (141502) esp-rest: File sending complete
I (141552) esp-rest: File sending complete
I (141912) esp-rest: File sending complete
```

Sometimes the log can show some warnings or errors because of the self-signed certifate after recieving a GET request:

```
I (205562) esp_https_server: performing session handshake
E (206642) esp-tls-mbedtls: mbedtls_ssl_handshake returned -0x7780
E (206642) esp_https_server: esp_tls_create_server_session failed
W (206642) httpd: httpd_accept_conn: session creation failed
W (206652) httpd: httpd_server: error accepting new connection
I (206652) esp_https_server: performing session handshake
I (208222) esp-rest: File sending complete
I (208842) esp-rest: File sending complete
I (208852) esp_https_server: performing session handshake
E (209962) esp-tls-mbedtls: mbedtls_ssl_handshake returned -0x7780
E (209962) esp_https_server: esp_tls_create_server_session failed
W (209972) httpd: httpd_accept_conn: session creation failed
W (209972) httpd: httpd_server: error accepting new connection
I (210982) esp-rest: File sending complete
I (210992) esp_https_server: performing session handshake
E (212092) esp-tls-mbedtls: mbedtls_ssl_handshake returned -0x7780
E (212092) esp_https_server: esp_tls_create_server_session failed
W (212092) httpd: httpd_accept_conn: session creation failed
W (212102) httpd: httpd_server: error accepting new connection
I (212152) esp-rest: File sending complete
I (212162) esp_https_server: performing session handshake
E (213272) esp-tls-mbedtls: mbedtls_ssl_handshake returned -0x7780
E (213272) esp_https_server: esp_tls_create_server_session failed
W (213272) httpd: httpd_accept_conn: session creation failed
W (213282) httpd: httpd_server: error accepting new connection
I (213302) esp_https_server: performing session handshake
I (215012) esp-rest: File sending complete
I (215022) esp_https_server: performing session handshake
E (216132) esp-tls-mbedtls: mbedtls_ssl_handshake returned -0x7780
E (216142) esp_https_server: esp_tls_create_server_session failed
W (216142) httpd: httpd_accept_conn: session creation failed
W (216142) httpd: httpd_server: error accepting new connection
```

## Log

* Last compile: October 11t 2022.
* Last test: October 11, 2022.
* Last compile espidf version: v4.4.2

## License

Apache License, Version 2.0, January 2004.

## Version

`v 1.0.1`
