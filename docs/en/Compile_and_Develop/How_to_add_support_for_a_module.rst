How to Add Support for a Module
================================
  
The ESP-AT project supports multiple modules, and provides configuration for them in the :component:`factory_param_data.csv <customized_partitions/raw_data/factory_param/factory_param_data.csv>` table and the files in the :AT:`module_config` folder. See the table below for the supported platforms (chip series) and modules, as well as locations of the default configuration files.

.. list-table:: default configuration files
   :header-rows: 1
   :widths: 5 10 40

   * - Platform
     - Module
     - Default
   * - ESP32
     - - WROOM-32
       - PICO-D4
       - SOLO-1
       - MINI-1
     - - :AT:`module_config/module_esp32_default/sdkconfig.defaults`
       - :AT:`module_config/module_esp32_default/sdkconfig_silence.defaults`
   * - ESP32
     - WROVER-32
     - - :AT:`module_config/module_wrover-32/sdkconfig.defaults`
       - :AT:`module_config/module_wrover-32/sdkconfig_silence.defaults`
   * - ESP32
     - ESP32-D2WD
     - - :AT:`module_config/module_esp32-d2wd/sdkconfig.defaults`
       - :AT:`module_config/module_esp32-d2wd/sdkconfig_silence.defaults`
   * - ESP32
     - ESP32_QCLOUD
     - - :AT:`module_config/module_esp32_qcloud/sdkconfig.defaults`
       - :AT:`module_config/module_esp32_qcloud/sdkconfig_silence.defaults`
   * - ESP32-C3
     - MINI-1
     - - :AT:`module_config/module_esp32c3_default/sdkconfig.defaults`
       - :AT:`module_config/module_esp32c3_default/sdkconfig_silence.defaults`
   * - ESP32-C3
     - ESP32C3_QCLOUD
     - - :AT:`module_config/module_esp32c3_qcloud/sdkconfig.defaults`
       - :AT:`module_config/module_esp32c3_qcloud/sdkconfig_silence.defaults`

.. note::

  - When the ``silent mode`` in ``./build.py menuconfig`` is ``0``, the default sdkconfig corresponding to the module is ``sdkconfig.defaults``.
  - When the ``silent mode`` in ``./build.py menuconfig`` is ``1``, the default sdkconfig corresponding to the module is ``sdkconfig_silence.defaults``.

If you want to add support for an ESP module in your ESP-AT project, you need to modify those configuration files. The "ESP module" here means:

- A new module that is not supported by the default ESP-AT project. It can be

  - A new module of the existing platforms.
  - A new module of a new platform. However, adding support for it requires extra huge work, thus not recommended and not explained in this document.

- An existing module with different configuration.

The document uses an example to explain how to add support for an ESP module in the ESP-AT project. The example module is ESP32-WROOM-32 that uses SDIO instead of the default UART interface.

Add Module to factory_param_data.csv
------------------------------------

Open your local :component:`factory_param_data.csv <customized_partitions/raw_data/factory_param/factory_param_data.csv>`, insert a new row at the end, set the parameters as needed. In the example, we set ``platform`` to ``PLATFORM_ESP32``, ``module_name`` to ``WROOM32-SDIO``, as well as other parameters as follows (see :ref:`factory-param-type-csv` for what each parameter represents):

- platform: PLATFORM_ESP32
- module_name: WROOM32-SDIO
- description: 
- magic_flag: 0xfcfc
- version: 3
- reserved1: 0
- tx_max_power: 78
- uart_port: 1
- start_channel: 1
- channel_num: 13
- country_code: CN
- uart_baudrate: -1
- uart_tx_pin: -1
- uart_rx_pin: -1
- uart_cts_pin: -1
- uart_rts_pin: -1
- tx_control_pin: -1
- rx_control_pin: -1

Modify esp_at_module_info Structure
-----------------------------------

Refer to :ref:`modify-esp-at-module-info-structure` for details.

Configure the Module
---------------------

Firstly, enter ``module_config`` folder, and create a new folder to store all the configuration files for your module. Note that the folder name should be in lower case. Then, add the configuration files in the new folder: IDF_VERSION, at_customize.csv, partitions_at.csv, sdkconfig.defaults, and sdkconfig_silence.defaults.

In this example, we copy the ``module_esp32_default`` folder as well as the files within it and rename it as ``module_wroom32-sdio``. The copied IDF_VERSION, at_customize.csv, and partitions_at.csv do not need any modification in our case. We only need to modify the sdkconfig.defaults and sdkconfig_silence.defaults:

- Modify the two files to use the partition table in the ``module_wroom32-sdio`` folder as follows:

  ::

    CONFIG_PARTITION_TABLE_CUSTOM_FILENAME="module_config/module_wroom32-sdio/partitions_at.csv"
    CONFIG_PARTITION_TABLE_FILENAME="module_config/module_wroom32-sdio/partitions_at.csv"
    CONFIG_AT_CUSTOMIZED_PARTITION_TABLE_FILE="module_config/module_wroom32-sdio/at_customize.csv"

- Modify the two files to use the SDIO configuration and remove the UART configuration as follows:

  - Remove the UART configuration
  
    ::

      CONFIG_AT_BASE_ON_UART=y

  - Add the SDIO configuration

    ::

      CONFIG_AT_BASE_ON_SDIO=y

After finishing the above steps, you can recompile the ESP-AT project to generate the firmware for your module. In this example, we choose ``PLATFORM_ESP32`` and ``WROOM32-SDIO`` when configuring the project to generate the firmware for it.