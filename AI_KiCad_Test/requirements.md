# AI-Assisted STM32 Sensor Board — Requirements

## 1. Project Objective

Design a simple 2-layer embedded development and sensor PCB using KiCad.

The main purpose of this project is to test an AI-assisted PCB design workflow from requirements and datasheets through:

- schematic creation
- ERC verification
- footprint assignment
- PCB placement
- routing
- DRC verification
- manufacturing file generation

The PCB should be simple, easy to inspect manually, and suitable for manufacturing through a standard PCB manufacturer such as JLCPCB.

## 2. Main Components

The design should use the following main components:

### Microcontroller

- STM32G031K8T6
- Package: LQFP-32
- Manufacturer: STMicroelectronics

### Temperature Sensor

- TMP102
- Digital temperature sensor
- I2C interface
- Manufacturer: Texas Instruments

### Voltage Regulator

- TLV75533P
- Fixed 3.3 V LDO
- Input supplied from USB 5 V
- Manufacturer: Texas Instruments

### Power Input

- USB Type-C receptacle
- Used for power input only
- No USB data communication required
- No USB Power Delivery required
- Standard 5 V USB supply operation

## 3. System Architecture

The intended architecture is:

USB-C 5 V input
→ 3.3 V LDO
→ STM32G031K8 MCU
→ TMP102 temperature sensor through I2C

The PCB should also include:

- SWD programming/debug interface
- reset push button
- one user-controllable LED
- power indication LED if appropriate
- required decoupling capacitors
- required I2C pull-up resistors
- appropriate USB-C configuration resistors
- test points where useful

## 4. Power Requirements

### Input Power

The board should operate from a standard USB-C 5 V source.

USB-C is used only for power.

USB D+ and D- communication is not required.

The USB-C CC pins must be configured correctly for a simple USB Type-C power sink according to applicable USB-C requirements.

### 3.3 V Supply

The TLV75533P should convert USB 5 V to 3.3 V.

The LDO circuit must follow the manufacturer's datasheet recommendations regarding:

- input capacitor
- output capacitor
- enable pin
- grounding
- placement
- stability requirements

The 3.3 V rail supplies:

- STM32G031K8
- TMP102
- I2C pull-ups
- LEDs where appropriate

## 5. MCU Requirements

The STM32G031K8T6 should be used as the main controller.

The design must follow STMicroelectronics recommendations for:

- MCU power pins
- grounding
- decoupling capacitors
- reset circuitry
- SWD interface
- unused pins
- boot configuration
- clock configuration

Use the STM32G031K8 datasheet, STM32G0 hardware-design application note, and NUCLEO-G031K8 schematic as primary references.

The onboard ST-LINK circuit from the Nucleo reference board must NOT be reproduced.

An external ST-LINK programmer/debugger will be used.

## 6. Clock Requirements

Prefer the simplest valid clock architecture.

Use the STM32 internal oscillator unless there is a documented technical reason to require an external crystal.

Do not add an external oscillator unnecessarily.

Any clock-related decision must be justified using STM32 documentation.

## 7. SWD Programming and Debugging

Provide an SWD programming/debug interface for an external ST-LINK.

The interface should provide at least:

- 3.3 V reference
- GND
- SWDIO
- SWCLK
- NRST

Use a simple header suitable for prototype development.

The exact connector footprint may be selected during the design process, but it should be easy to probe and connect manually.

## 8. Reset Circuit

Provide a physical reset push button connected to the MCU NRST signal.

The reset circuit should follow STM32 hardware-design recommendations.

Do not add unnecessary components if the MCU already provides suitable internal circuitry.

Any external pull-up, capacitor, or filtering component must be justified using official documentation.

## 9. Temperature Sensor

Use TMP102 as the temperature sensor.

Connect it to the STM32 using I2C.

The design should include:

- SDA
- SCL
- 3.3 V supply
- GND
- required decoupling
- appropriate I2C pull-up resistors
- deterministic ADD0 configuration

The ALERT output is optional and may remain unused unless there is a clear benefit to connecting it to an MCU GPIO.

If ALERT is not used, handle the pin according to the TMP102 datasheet.

## 10. I2C Interface

Select valid STM32 pins supporting I2C.

Prefer a simple pin mapping that does not interfere with SWD or other essential functions.

Use external pull-up resistors on SDA and SCL.

The initial target pull-up value may be approximately 4.7 kΩ, but the final value must be verified against:

- bus voltage
- expected bus capacitance
- target I2C frequency
- relevant device recommendations

The intended I2C speed is 100 kHz or 400 kHz.

## 11. User LED

Include one MCU-controlled user LED.

The LED should:

- be driven from a standard GPIO
- include an appropriate current-limiting resistor
- use a common SMD package
- avoid excessive GPIO current

Prefer a circuit that is logically intuitive, for example:

GPIO → resistor → LED → GND

unless another configuration is preferable.

## 12. Power Indicator

A separate power LED may be included on the 3.3 V rail.

If included:

- keep its current consumption modest
- use an appropriate current-limiting resistor
- ensure it does not significantly affect low-power operation

This LED is optional.

## 13. PCB Requirements

### Layer Count

- 2-layer PCB

### Approximate Board Size

Target approximately:

- 40 mm × 30 mm

The dimensions can be adjusted slightly if required for reasonable component placement.

### General Placement

Prefer:

- USB-C connector at a PCB edge
- SWD connector at an accessible edge
- reset button easily accessible
- LEDs visible from the top
- TMP102 away from significant heat-generating components where practical
- LDO close to its input/output capacitors
- MCU decoupling capacitors placed very close to MCU power pins

## 14. Grounding

Use a solid ground strategy.

Prefer large uninterrupted GND copper areas.

Avoid unnecessary fragmentation of the ground return path.

Use ground vias where useful.

The final board should maintain short return paths for:

- MCU decoupling
- LDO capacitors
- sensor decoupling
- digital signals

## 15. Routing Requirements

Critical connections should be routed intentionally before any automated routing.

Prioritize:

1. USB 5 V power path
2. LDO input and output
3. MCU power and ground
4. decoupling capacitors
5. SWD
6. I2C
7. remaining GPIO signals

Keep power traces appropriately sized.

Signal traces may use standard low-current PCB widths appropriate for the chosen PCB manufacturer's capabilities.

Avoid unnecessary vias.

## 16. Component Packages

Prefer commonly available SMD packages.

Where practical:

- resistors: 0603
- capacitors: 0603
- MCU: LQFP-32
- LDO: datasheet-recommended SOT-23 package
- LEDs: 0603 or similar

Avoid extremely small packages such as 0201 for this prototype.

Prefer components that are easy to hand inspect and potentially hand solder.

## 17. PCB Manufacturing Assumptions

The design should be compatible with common low-cost PCB fabrication capabilities.

Assume conservative manufacturing rules unless the manufacturer specifies otherwise.

The design should not rely on:

- blind vias
- buried vias
- microvias
- controlled impedance
- extremely small traces
- extremely small spacing

## 18. ERC and DRC Requirements

Before the design is considered complete:

### Schematic

- ERC must be run
- errors must be investigated
- warnings must not simply be suppressed without explanation

### PCB

- DRC must be run
- all significant violations must be resolved
- unconnected item count must be zero
- schematic and PCB connectivity must match

Passing ERC and DRC does not replace engineering review.

## 19. Footprint Verification

Every footprint must be checked against the corresponding datasheet.

Verify at minimum:

- package type
- pin numbering
- pad numbering
- pin 1 location
- orientation
- polarity
- package dimensions
- land-pattern compatibility

Do not assume a footprint is correct based only on its name.

## 20. Datasheet-Driven Design

Official manufacturer documentation should take priority over assumptions.

Primary references include:

- STM32G031K8 datasheet
- STM32G0 hardware-design application note
- NUCLEO-G031K8 schematic
- TLV755P datasheet
- TMP102 datasheet
- USB Type-C power-sink guidance

Whenever a design decision is based on a datasheet recommendation, identify the relevant source.

If documentation is ambiguous, flag the decision for human review.

## 21. Design Constraints for AI Agent

The AI agent must NOT:

- invent undocumented pin connections
- guess footprint pin numbering
- suppress ERC/DRC errors simply to make checks pass
- reproduce the complete Nucleo board
- add onboard ST-LINK circuitry
- add USB communication circuitry
- add USB Power Delivery circuitry
- use an external MCU crystal unless justified
- select exotic components unnecessarily

When uncertain, the AI should clearly identify the uncertainty instead of making an unsupported assumption.

## 22. Initial Design Review Gate

Before creating or modifying KiCad files, first provide:

1. complete system architecture
2. exact component part numbers
3. complete MCU pin assignment
4. power architecture
5. MCU decoupling network
6. reset circuit
7. SWD circuit
8. I2C circuit
9. TMP102 configuration
10. USB-C power-input circuit
11. LDO circuit
12. LED circuits
13. complete pin-to-pin connection table
14. footprint recommendations
15. datasheet references supporting important decisions
16. list of uncertainties requiring human review

Do not proceed to schematic creation until this engineering review has been completed.

## 23. Expected Final Deliverables

The completed project should eventually contain:

- KiCad project
- KiCad schematic
- KiCad PCB
- verified footprints
- ERC report/check
- DRC report/check
- Gerber files
- drill files
- BOM
- component position / pick-and-place file
- brief README describing the board
