# Brief of all code/config

<button id="toggle-admonitions" class="md-button">Collapse All/Expand All Code</button>

<script>
  document.getElementById('toggle-admonitions').addEventListener('click', function (event) {

    event.preventDefault();
    var admonitions = document.querySelectorAll('details');
    var anyOpen = false;

    var button = document.getElementById('toggle-admonitions');
    admonitions.forEach(function (admonition) {
      var admonitions = document.querySelectorAll('details');
      admonitions.forEach(function (admonition) {
        if (button.textContent == 'Collapse All') {
          if (admonition.hasAttribute('open')) {
            admonition.removeAttribute('open');
          }
        }
        else {
          admonition.setAttribute('open', '');
          anyOpen = true;
        }
      });
    });

    if (anyOpen) {
      button.textContent = 'Collapse All';
    } else {
      button.textContent = 'Expand All';
    }

  });

</script>

## ~PATHs

Device tree file

```sh
<install_path>\zephyr\boards\arm\<board_name>
```

## `CMakeLists.txt`

```apacheconf
cmake_minimum_required(VERSION 3.20.0)
find_package(Zephyr REQUIRED HINTS $ENV{ZEPHYR_BASE})
project(nrf_connect_sdk_fundamentals)

target_sources(app PRIVATE src/main.c)
# target_sources_ifdef(CONFIG_MYFUNCTION app PRIVATE src/myfunction.c) # myfunction.c (in this case) in the same folder as main.c
```

??? example "myfunction.c & myfunction.h"

    - myfunction.c

    ```cpp
    #include "myfunction.h"

    int sum(int a, int b){
        return a+b;
    }
    ```

    - myfunction.h

    ```cpp
    #ifndef MY_FUNCTION_H
    #define MY_FUNCTION_H

    int sum(int a, int b);

    #endif
    ```

## `prj.conf`

```apacheconf
## IO
CONFIG_GPIO=y
CONFIG_SERIAL=y                 ##Enable the serial driver in asynchronous mode 
CONFIG_UART_ASYNC_API=y         # ↑
CONFIG_I2C=y                    # Enable the I2C driver

CONFIG_CBPRINTF_FP_SUPPORT=y    # Enable floating point format specifiers
CONFIG_PRINTK=y                 # Enable the printk function
CONFIG_ENTROPY_GENERATOR=y
CONFIG_TEST_RANDOM_GENERATOR=y

## LOGS
CONFIG_LOG=y                    # Enable logger module
CONFIG_LOG_BACKEND_SHOW_COLOR=n # Disable colors in the backend
CONFIG_LOG_MODE_MINIMAL=y       # Enable minimal logging

## CUSTOM
CONFIG_MYFUNCTION=y # allows: #ifdef CONFIG_MYFUNCTION

## THREADS
CONFIG_TIMESLICING=y            ##enable timeslicing
CONFIG_TIMESLICE_SIZE=10        # ↑
CONFIG_TIMESLICE_PRIORITY=0     # ↑
```

## `.overlay`

In the `Details View`, there is an option to create an overlay file with the same board name used for the build. To apply changes do `pristine build`.
In the same folder as prj.conf, eg: `nrf52833dk_nrf52833.overlay`.

```sh
&uart0 {
 current-speed = <9600>;
};
```

## `Kconfig.zephyr`

Configuring system parameters

??? example "MYFUNCTION"

    ```sh
    source "Kconfig.zephyr"
    config MYFUNCTION
        bool "Enable my function"
        default n
    ```

    - `source "Kconfig.zephyr"`: This line includes the contents of the file named "Kconfig.zephyr". This is typically used to include configuration
    options from another file.

    - `config MYFUNCTION`: This line declares a new configuration option named "MYFUNCTION".

    - `bool "Enable my function"`: This line sets the type of the configuration option to boolean, and provides a prompt string "Enable my function"
    that will be displayed to the user when they are configuring the system.

    - `default n`: This line sets the default value of the configuration option to 'n' (no). If the user does not explicitly set this option,
    it will be 'n'.

## `main.c`

??? example "blinky - pooling data"
    <!--codeinclude-->
    [](./briefcode/blinky.c)
    <!--/codeinclude-->

??? example "blinky2 - button with callback"
    <!--codeinclude-->
    [](./briefcode/blinky2.c)
    <!--/codeinclude-->

??? example "myfun - enabled MYFUNCTION (need `Kconfig.zephyr` and `myfunction.h & c`)"
    <!--codeinclude-->
    [](./briefcode/myfun.c)
    <!--/codeinclude-->

??? example "factorial - (ISR)Calculate and print factorials after button click"
    <!--codeinclude-->
    [](./briefcode/factorial.c)
    <!--/codeinclude-->

??? example "factorial_log - (ISR + Logger)Calculate and print factorials after button click"
    <!--codeinclude-->
    [](./briefcode/factorial_log.c)
    <!--/codeinclude-->

??? example "factorial_logh - (ISR + Logger) hexdump dbg data, Calculate and print factorials after button click"
    <!--codeinclude-->
    [](./briefcode/factorial_logh.c)
    <!--/codeinclude-->

??? example "key_ctrl - Controlling LEDs through UART. Press 1-3 on your keyboard (Enable the serial driver in asynchronous mode)"
    <!--codeinclude-->
    [](./briefcode/key_ctrl.c)
    <!--/codeinclude-->

??? example "sen_i2c - get data from i2c temp sensor SSTS751 (Enable CONFIG_I2C & CONFIG_CBPRINTF_FP_SUPPORT)"
    <!--codeinclude-->
    [](./briefcode/sen_i2c.c)
    <!--/codeinclude-->

??? example "sen2_i2c - get data from i2c RGB sensor BH1749 (Enable CONFIG_I2C & CONFIG_CBPRINTF_FP_SUPPORT)"
    <!--codeinclude-->
    [](./briefcode/sen2_i2c.c)
    <!--/codeinclude-->

??? example "thrd - threads creation (Enable CONFIG_PRINTK)"
    <!--codeinclude-->
    [](./briefcode/thrd.c)
    <!--/codeinclude-->

??? example "thrd_ts - threads creation with timeslicing (Enable CONFIG_PRINTK, timeslicing)"
    <!--codeinclude-->
    [](./briefcode/thrd_ts.c)
    <!--/codeinclude-->

??? example "thrd_wq - workqueue threads creation (Enable CONFIG_PRINTK)"
    <!--codeinclude-->
    [](./briefcode/thrd_wq.c)
    <!--/codeinclude-->

??? example "thrd_semaphore - producer & consumer problem with `semaphore` (Enable CONFIG_PRINTK, CONFIG_ENTROPY_GENERATOR, CONFIG_TEST_RANDOM_GENERATOR)"
    <!--codeinclude-->
    [](./briefcode/thrd_semaphore.c)
    <!--/codeinclude-->

??? example "thrd_mutex - race condition problem with `mutex` (Enable CONFIG_PRINTK, CONFIG_ENTROPY_GENERATOR, CONFIG_TEST_RANDOM_GENERATOR, timeslicing)"
    <!--codeinclude-->
    [](./briefcode/thrd_mutex.c)
    <!--/codeinclude-->
