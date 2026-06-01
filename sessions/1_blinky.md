# Working with the Zephyr blinky sample

Agenda:
* Getting familiar with overlay files
* Changing the LED Pin through DTS modifications
* Adding a custom kconfig definitions to modify the application logic
* Using a Zephyr system work queue
<br>

## Task 1: Creating the overlay file

For Nordic nRF54L15-DK:
`nrf54l15dk_nrf54l15_cpuapp.overlay`

For the u-blox EVK-NORA-B2:
`ubx_evknorab2_nrf54l15_cpuapp.overlay`
<br>

> [!IMPORTANT]
> When creating an overlay in VS Code, the build tools will detect file changes and automatically prompt for a CMake invocation and pristine build. Click **Skip** first, complete all overlay modifications, and run the pristine build only afterwards.

## Task 2: DTS led binding
Choose either option A, B or C, or try them out one after another!

Hardware setup
<table>
<tr>
<td valign="top">

| HW / Board | LED | Port, Pin |
|-------------|-----|-----------|
| nRF54L15-DK| LED0 | Port 2, Pin 9 |
| nRF54L15-DK| LED1 | Port 1, Pin 10 |
| nRF54L15-DK| LED2 | Port 2, Pin 7 |
| nRF54L15-DK| LED3 | Port 1, Pin 14 |

</td>
<td valign="top">

| HW / Board | LED | Port, Pin |
|-------------|-----|-----------|
| EVK-NORA-B2 | LED0 / red | Port 2, Pin 9 |
| EVK-NORA-B2 | LED1 / green | Port 2, Pin 7 |
| EVK-NORA-B2 | LED2 / blue | Port 1, Pin 10 |

</td>
</tr>
</table>

#### Option A - Modify Existing `led0` node

```dts
&led0 {
    gpios = <&gpio2 7 GPIO_ACTIVE_HIGH>;
};
```
> [!TIP]
> Done with Option A, no source code change required. Rebuild, flash & test.

#### Option B -  Creating a new DTS node group

Add a new LED node group to your overlay `nrf54l15dk_nrf54l15_cpuapp.overlay` OR `ubx_evknorab2_nrf54l15_cpuapp.overlay`:

```dts
/ {
    board_leds {
        compatible = "gpio-leds";

        my_led_1: my_led1 {
            gpios = <&gpio1 10 GPIO_ACTIVE_HIGH>;
            label = "My custom led";
        };
    };
};
```

Modify `main.c` and obtain access to the new LED node:

```c
#define LED0_NODE DT_PATH(board_leds, my_led1)
```

> [!TIP]
> Done with Option B. Rebuild, flash & test.

#### Option C - New DTS node group with alias usage

We'll use a custom LED node group, but add an alias to not require any source code change.

```dts
/ {
    board_leds {
        compatible = "gpio-leds";

        my_led_1: my_led1 {
            gpios = <&gpio1 10 GPIO_ACTIVE_HIGH>;
            label = "My custom led";
        };
    };
    aliases {
        led0 = &my_led_1;
    };
};
```

> [!TIP]
> Done with Option C, no source code change required. Rebuild, flash & test.

## Task 3: Adding Custom Kconfig Definitions

#### Step 1 - Creating a custom kconfig menu

Create a new file in your project root directory named: `Kconfig`

Add the following menu entry:

```ini
menu "Blinky at Workshop"

config BLINKY_TIME_ON
    int "The time the LED is on (ms)."
    default 1000

endmenu

menu "Zephyr Kernel"
source "Kconfig.zephyr"
endmenu
```

#### Step 2 - Utilizing the new kconfig menu

Add the new kconfig setting to your project's kconfig `prj.conf`:

```ini
CONFIG_BLINKY_TIME_ON=500
```

#### Step 3 - Source code modification

Replace the following line in `src/main.c`:

```c
k_msleep(SLEEP_TIME_MS);
```

with:

```c
k_msleep(CONFIG_BLINKY_TIME_ON);
```

> [!TIP]
> Done. Rebuild, flash & test.

## Task 4: Using the Zephyr System Work Queue

#### Step 1

Add a work queue definition in `main.c`:

```c
static struct k_work_delayable blink_led_work;
```


#### Step 2

Create the work queue handler function:

```c
static void blink_led_work_fn(struct k_work *work)
{
    int ret;

    ret = gpio_pin_toggle_dt(&led);
    if (ret < 0) {
        return;
    }

    k_work_schedule(&blink_led_work, K_MSEC(CONFIG_BLINKY_TIME_ON));
}
```

#### Step 3

Create a function to initialize the work queue:

```c
static void work_init(void)
{
    k_work_init_delayable(&blink_led_work, blink_led_work_fn);
}
```

#### Step 4

Remove the `while(1)` loop in `main(void)` and replace it with:

```c
work_init();
k_work_schedule(&blink_led_work, K_NO_WAIT);
```

> [!TIP]
> Done. Rebuild, flash & test.