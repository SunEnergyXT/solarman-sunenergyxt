# solarman-sunenergyxt
Modified Solarman integration for Home Assistant with register 53 support for active power regulation

> **Important Notice**: This repository is a modified version of [davidrapan/ha-solarman](https://github.com/davidrapan/ha-solarman). The original code copyright belongs to the respective author. This modification is made solely to address specific device compatibility issues.

## 🔧 Modification Reason and Technical Explanation

In the original Solarman component, the "Active Power Regulation" feature that controls the power output of Deye inverters corresponds to the inverter's **Register 40** (address 0x0028). Through practical testing, we discovered:

- Register 40 does not support setting the power output to **0%**
- The minimum settable value is only 1%, making zero feed-in impossible to achieve
- The inverter's **Register 53** (address 0x0035) supports adjustment down to 0% output
- Register 53 is more suitable for precise power control requirements

## Minimum Written Value of Active Power Regulation

Writing exactly **0%** to Register 53 makes the Deye microinverter stop its output, enter the Fault state and report **DC Bus2 Uneven Voltage**. Zero feed-in automations that frequently send 0% therefore produce many fault messages, although the inverter recovers on its own.

- Any value below **0.1%** (including 0%) is written as **0.1%**, so automations can still send 0% without an error
- At 0.1% the inverter keeps running normally and the output is practically zero (about 0.8 W on an 800 W model)
- To switch the inverter off completely, use the inverter's On/Off switch entity
- If the value is already 0.1% and you enter 0 again on the HA page, the input box may keep showing 0 because the state does not change; refresh the page to see the actual value 0.1%
