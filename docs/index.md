# nRF Connect SDK Fundamentals

[Documentation](https://developer.nordicsemi.com/nRF_Connect_SDK/doc/latest/nrf/index.html)

`RTOS` - serve real-time applications that process data as it comes in, typically without buffer delays. These applications include embedded systems,
telecommunications, and industrial automation systems.

The key features of an RTOS include:

- Deterministic Scheduling: RTOSs provide deterministic scheduling, meaning that the system can predict the exact time at which a task will be executed.
This is crucial for systems where timing is critical.

- Preemptive Scheduling: RTOSs use preemptive scheduling, which means that a high-priority task can interrupt a lower-priority task. This is important
for systems where certain tasks must be completed before others.

- Interrupt Handling: RTOSs provide efficient interrupt handling, which is crucial for systems that need to respond quickly to external events.

- Task Prioritization: RTOSs allow tasks to be prioritized, which means that tasks can be assigned different levels of importance. This is important
for systems where some tasks are more important than others.

- Real-Time Performance: RTOSs are designed to provide real-time performance, which means that they can process data as it comes in, typically without
buffer delays. This is important for systems where timing is critical.

In conclusion, RTOSs are designed for systems where timing is critical and where tasks must be executed in a specific order. They provide features such
as deterministic scheduling, preemptive scheduling, efficient interrupt handling, task prioritization, and real-time performance, which are crucial
for these types of systems.

`Zephyr OS` is a real-time operating system (RTOS) designed for resource-constrained systems, particularly in the Internet of Things (IoT) domain.
It is designed to be lightweight, efficient, and secure, with a focus on energy efficiency

Here are some key differences between Zephyr OS and consumer-based operating systems:

- Design and Purpose: Zephyr OS is designed for resource-constrained systems, particularly in the IoT domain, where energy efficiency is crucial.
It is designed to be lightweight and efficient, focusing on energy efficiency. Consumer-based operating systems like Windows or Ubuntu are designed
for a wide range of applications, from personal computing to server environments, and are not designed to be lightweight or energy-efficient.

- Security: Zephyr OS is designed with a focus on security. It provides long-term support, regular security updates, and a rigorously tested and
auditable code base. Consumer-based operating systems like Windows or Ubuntu also prioritize security, but they are not designed with the same level of
focus as Zephyr OS.

- Interoperability: Zephyr OS is designed to be interoperable across a wide range of hardware, software, and communication protocol options.
Consumer-based operating systems like Windows or Ubuntu are designed to be compatible with a wide range of hardware and software, but they are not
designed with the same level of interoperability as Zephyr OS.

- Ecosystem: Zephyr OS has a strong ecosystem, including a community that provides training and consulting, development tools, code libraries, and more.
Consumer-based operating systems like Windows or Ubuntu also have strong ecosystems, but they are not designed with the same level of community support
and development tools as Zephyr OS.

In conclusion, while Zephyr OS and consumer-based operating systems like Windows or Ubuntu serve different purposes and have different characteristics,
they both play crucial roles in the world of computing. The choice between them depends on the specific requirements of the application or system
being developed.

## Lesson 1

<img src="./assets/nRF_connect_sdk_2-01.png" alt="Image description"
style="display: block; margin: auto; width: 65%; height: auto; border-radius: 8px;">

`Zephyr RTOS` is an open-source real-time operating system for connected and resource-constrained embedded devices. It includes a scheduler that ensures
predictable/deterministic execution patterns and abstracts out the timing requirements. It also comes with a rich set of fundamental libraries and middleware
that simplifies development and helps reduce a product’s time to market. Zephyr RTOS is highly configurable and enables scalable configurations from
very small configurations for memory-constrained devices (minimum 8 kilobytes, for example, simple LED blinking application) to powerful, feature-rich,
high-processing power devices (multiple MBs of memory) with large memory configurations.

Internally, the nRF Connect SDK code is organized into four main repositories:

- nrf – Applications, samples, connectivity protocols (Nordic)
- nrfxlib – Common libraries and stacks (Nordic)
- Zephyr – RTOS & Board configurations (open source)
- MCUBoot – Secure Bootloader (open source)

<img src="./assets/Lesson_1_build_process-04.png" alt="Image description"
style="display: block; margin: auto; width: 85%; height: auto; border-radius: 8px;">

`west` is a core command-line utility that is internally invoked by the nRF Connect for VS Code to do many tasks including building, and flashing
applications to your board.

The `devicetree` describes the hardware and `Kconfig` generates definitions that configure the whole system.

## Lesson 2

examine how hardware is described in nRF Connect SDK, whether it is a development kit (DK), a System on Chip (SoC), a System in a Package (SiP).

Objectives

- Examine the devicetree API <zephyr/devicetree.h>
- Examine board-level devicetree .dts
- Examine SoC-level devicetree .dtsi
- Understand the purpose of devicetree binding files (.yaml) and the compatible property
- Understand the device driver model <zephyr/device.h>
- Analyze the decoupling between a device driver API and a device driver implementation and the need to have a device pointer
- Examine the generic GPIO interface APIs <zephyr/drivers/gpio.h>
- Practice through hands-on exercises configuring GPIO pins and learn how to read/write to/from GPIO pins and how to set up interrupts for input GPIO pins

### Devicetree

```sh
/dts-v1/;
/ {
        a-node {
                subnode_label: a-sub-node {
                        foo = <3>;
                };
        };
};
```

#### Devicetree bindings (YAML files)

It declares requirements on the contents of devicetree nodes, and provides semantic information about the contents of valid nodes.

```yaml
compatible: "nordic,nrf-sample"
properties:
  num-sample:
    type: int
    required: true
```

Sample DTS file (.dts) with the node `node0` that is set to the compatible `nordic,nrf-sample`. This means the `node0` node must have the required property
`num-sample` and that property must be assigned an integer value. Otherwise, the build will fail.

```sh
node0 {
     compatible = "nordic,nrf-sample";
     num-sample = <3>;
};
```

#### Aliases

name of the property is the name of that alias and the value of the property is a reference to a node in the device tree.

```sh
/ {
        aliases {
                subnode_alias = &subnode_label;
        };
};
```

The purpose here is that your C/C++ application code (Ex: main.c) will use the alias. The definition of fixed aliases (Ex: led0 for the first LED on a
board ) in boards’ dts files can make the application code more portable, as it can avoid hard-coding varying device node names and make the application
code more flexible to changes in the board used.

#### Accessing the devicetree

[Ways to get node-identifier](https://developer.nordicsemi.com/nRF_Connect_SDK/doc/latest/zephyr/build/dts/api-usage.html#node-identifiers)

To get information about a particular devicetree node in your source code, you need a node identifier for it. This is just a C macro that refers to
the node.

node identifier of a-sub-node:

```sh
DT_NODELABEL(subnode-label)
```

To get the value assigned to a certain devicetree property, we can use the macro DT_PROP().

```sh
DT_PROP(DT_NODELABEL(subnode-label), foo)
```

Device tree file is available in path: `<install_path>\zephyr\boards\arm\nrf52833dk_nrf52833\nrf52833dk_nrf52833.dts.`

### Device driver model

In order to interact with a hardware peripheral or a system block, we need to use a device driver (or driver for short), which is software that deals
with the low-level details of configuring the hardware the way we want.

The following code snippet will take the devicetree node identifier returned by DT_NODELABEL() and return a pointer to the device object.
Then device_is_ready() verifies that the device is ready for use, i.e. in a state so that it can be used with its standard API.

```cpp
const struct device *dev;
dev = DEVICE_DT_GET(DT_NODELABEL(uart0));
if (!device_is_ready(dev)) {
    return;
}
```

### GPIO Generic API

To interact with the General-Purpose Input/Output (GPIO) peripheral, we can use the generic API <zephyr/drivers/gpio.h>, which provides user-friendly
functions to interact with GPIO peripherals.

When using any driver in Zephyr, the first step is to initialize it by retrieving the device pointer.

#### Initializing the API

