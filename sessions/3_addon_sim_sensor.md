# Addon: Adding a simulated sensor driver

1. In `prj.conf`, enable the sensor driver and floating-point formatting.

    Add:

    ```ini
    # Enable Sensor Driver
    CONFIG_SENSOR=y
    CONFIG_CBPRINTF_FP_SUPPORT=y
    ```

2. If no overlay file is present yet, create one e.g. `nRF54l15dk_nrf54l15_cpuapp.overlay`

    Add the simulated sensor node to your dts overlay:

    ```dts
    / {
        sensor_sim: sensor_sim {
            compatible = "nordic,sensor-sim";
            status = "okay";
            acc-signal = "wave";
            base-temperature = <20>;
            base-pressure = <98>;
        };
    };
    ```

3. Add the header file and define for the sensor node label pointer:
    ```c
    #include <zephyr/drivers/sensor.h>
    ```
    ```c
    #define SENSOR_NODE DT_NODELABEL(sensor_sim)
    ```

4. Add the sensor data processing function to your main code, e.g. `main.c`:

    ```c
    static void process_sample(const struct device *dev)
    {
        struct sensor_value pressure, temp;

        // Fetch a sample from the sensor and store it in an internal driver buffer
        if (sensor_sample_fetch(dev) < 0) {
            printk("Error: Sensor sample update error\n");
            return;
        }

        // Get a reading from a sensor device (read pressure)
        if (sensor_channel_get(dev, SENSOR_CHAN_PRESS, &pressure) < 0) {
            printk("Error: Cannot read pressure channel\n");
            return;
        }

        // Get a reading from a sensor device (read temperature)	
        if (sensor_channel_get(dev, SENSOR_CHAN_AMBIENT_TEMP, &temp) < 0) {
            printk("Error: Cannot read temperature channel\n");
            return;
        }

        /* display pressure */
        printk("pressure: %d.%d kPa, ", pressure.val1, pressure.val2);
        /* display temperature */
        printk("Temperature: %.1f C\n", sensor_value_to_double(&temp));
    }
    ```

5. In `main.c` (before your main loop <code>for (;;)</code>): Add initialization of the simulated sensor:
   
   ```c
    const struct device *const dev = DEVICE_DT_GET(SENSOR_NODE);
    if (!device_is_ready(dev)) {
        printk("Sensor is not ready %s\n", dev->name);
        return 0;
    }
    printk("Sensor is ready!\n");
    ```

6. In `main.c` (within the main loop <code>for (;;)</code> loop): Call processing function

    ```c
    process_sample(dev);
    ```


   
