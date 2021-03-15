# EmSys_Lab3


## The temperature sensor

### wiring diagram

### datasheet
https://datasheets.maximintegrated.com/en/ds/DS18B20.pdf

### Arduino Library


## Data transfer protocol formats 

There are two different formats that you can use to transfer the temperature data from your device to the central server. 

* A JSON based protocol, similar to what you used to send commands for your dotDevice
* A binary protocol 

Each transfer method has a function in ``LetESP32.h`` to help with transfering data.

I would probably recomend starting with the JSON protocol first, and moving onto the binary protocol later when you feel confident.

### JSON based protocol

The standard format to send over your temperature data is via a json format.
This can be constructed using the ``String`` class in a similar fashion to what you did for your dotDevices in lab 1.
However, instead of a small command these JSON strings are a bit longer.

They contain, your unique device name, the average temperature (you must calculate these on your device), and a list of 16 temperature readings. Each of the 16 temperature readings contains a relative timestamp and the associated value for the reading. 

```json
{
   "device": "wibble00",
   "average": 19.4,
   "values":[ 
        {"timestamp" : 1034, "value": 19.5},
        {"timestamp" : 1134, "value": 19.4},
        {"timestamp" : 1234, "value": 19.2},
        {"timestamp" : 1334, "value": 19.4},
        {"timestamp" : 1434, "value": 19.5},
        {"timestamp" : 1534, "value": 19.4},
        {"timestamp" : 1634, "value": 19.2},
        {"timestamp" : 1734, "value": 19.5},
        {"timestamp" : 1834, "value": 19.4},
        {"timestamp" : 1934, "value": 19.2},
        {"timestamp" : 2034, "value": 19.5},
        {"timestamp" : 2134, "value": 19.4},
        {"timestamp" : 2234, "value": 19.2},
        {"timestamp" : 2334, "value": 19.5},
        {"timestamp" : 2434, "value": 19.4},
        {"timestamp" : 2534, "value": 19.2}
  ] 
}
```

To transfer JSON data to the host server use the following `LetESP32.h` method.

```C
LetESP32 tracer(ssid, password, ws, "wibble00");

void loop() {
   tracer.sendJSON(json_str); // where json_str is a String containing the command payload
}
```

### Binary protocol

```C
struct temp_t {     
    uint16_t t;        
    uint16_t id;     
};                   
                                              
// temp buffer type                           
struct buffer_t {    
        char ident[8]; // dotDevice identifiers are 8 chars -- 64bits    
        uint16_t cmd;    
        uint16_t avr; // the average temp    
        temp_t buff[16];    
};    
```

The binary protocol uses fixed-point notation for the temperature values, and aims to pack the temperature values as tightly as possible to minimise overheads when transferring the data. 

To send the binary protocol data use the following ``LetESP32.h`` method:

```C
LetESP32 tracer(ssid, password, ws, "wibble00");

void loop() {
   tracer.sendBIN(tmp_data);
}
```
