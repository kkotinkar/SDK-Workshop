# Bluetooth LE

## Task 1: Adding Bluetooth LE Advertising


1. Add the bluetooth stack to your software configuration, by adding to `prj.conf`:

    ```ini
    CONFIG_BT=y
    ```

2. Include Bluetooth header files, e.g. when working with the Blinky sample within `main.c`:

    ```c
    #include <zephyr/bluetooth/bluetooth.h>
    ```

3. Construct the advertising packet, change the device name to your liking:

    ```c
    #define DEVICE_NAME "My BT Device"

    static const struct bt_data ad[] = {
       BT_DATA_BYTES(BT_DATA_FLAGS, (BT_LE_AD_GENERAL | BT_LE_AD_NO_BREDR)),
       BT_DATA(BT_DATA_NAME_COMPLETE, DEVICE_NAME, sizeof(DEVICE_NAME)-1),
    };
    ```

    > [!TIP] 
    >  The BLE adv packet can hold a total of 31 bytes. 3 bytes are taken to indicate BT data flags (LE only, no BR/EDR support) + 2 bytes are taken to indicate BT complete local Name (0x09) and the length of the name. This leaves a maximum of 26 bytes left for the actual device name.

4. Add to `main.c`: Initialize the Bluetooth stack and start advertising in <code>int main(void)</code>
   
   ```c
   // enable and init BTLE stack
   int err = bt_enable(NULL);

   // start advertising as non-connectable
   err = bt_le_adv_start(BT_LE_ADV_NCONN, ad, ARRAY_SIZE(ad), NULL, 0);
   if (err) {
       printk("Advertising failed to start (err %d)\n", err);
       return -1;
   }
   printk("Advertising started\n");
    ```
   
## Task 2: Working with the peripheral_lbs sample

Start with a new project, using the Nordic Peripheral LBS sample, found under <code>sdk/nrf/samples/bluetooth/peripheral_lbs</code>

1. In `main.c`: Change the scan response packet, and include a URL to a website using a Uniform Resource Identifier

    ```c
    #define URL_STRING "//academy.nordicsemi.com"

    static const struct {
        unsigned char prefix;
        const char url_string[sizeof(URL_STRING)];
    } url_data = {
        .prefix = 0x17,
        .url_string = URL_STRING
    };

    static const struct bt_data sd[] = {
        BT_DATA(BT_DATA_URI,
                (const unsigned char *)&url_data,
                sizeof(url_data))
    };
    ```
