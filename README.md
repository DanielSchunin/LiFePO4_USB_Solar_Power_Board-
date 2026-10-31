# LiFePO4 USB/Solar Power Board
This is a compact battery and power board for small, solar-energy-autonomous, microcontroller-based projects. The STMicroelectronics SPV1050TTR allows for harvesting almost all energy from a small solar panel or thermal generator throughout the entire day and charging a battery. This is perfect for sleep-heavy microcontroller applications, like periodically reading sensors or updating an e-paper display. A USB-C port easily allows topping up the battery at will.

![Board Image Front](<./lifepo4_usb_solar_power_board PCB 3D Print front.png>)

![Board Image Back](<./lifepo4_usb_solar_power_board PCB 3D Print back.png>)

## Specs
Currently set up for the following specs:
> Input: Up to 6 V, 60 mA
> 
> Output: 1.8 V and 3.3 V with a 100 mA current limit
> 
> Direct output of the battery voltage, tuned for a LiFePO4 battery with a maximum voltage of 3.6 V down to the brownout voltage of an ESP32 at 2.5 V.
> 
> Signal outputs for the safe shutdown of applications that use the direct battery connections, due to the amperage limits of the 3.3 V and 1.8 V rails.
> 
> Direct connection of the battery to the board over a JST connector.
> 
> Max input of 20 V, the chip does have a 70 mA charging current limit.
> 
> 34mm x 18mm compact formfactor with two M2 mounting holes

However, the input and battery voltages can be easily configured by changing out the 0805 R1-R6 resistors; see "Resistor Network Calculations".

## Manufacturing
This board is optimized to be partially populated by JLCPCB and then finished by hand. Common resistors and capacitors can be populated cheaply, while non-standard values like the R1-R6 configuration have large footprints to be easily hand-soldered.
Ports, headers, R1-R6, and the inductor are meant to be hand-soldered, while it is recommended to pay the fee to let JLCPCB solder the main SPV1050TTR chip due to the small pad sizes.
This project is optimized for JLCPCB's base profile with no upcharge options.

##Part Choices
Ideally, the solar input diode would be replaced with an ideal diode, such as a MAX40200. However, the chosen solar panel reaches 6 V on a sunny day, which is over the V_(max) of 5.5 V of that particular chip.
An alternative with a higher voltage, such as the NSR0520V2, would be ideal. However, these are currently out of stock with long lead times. The PMEG2010BER,115 diode is inexpensive and has acceptable small losses.

## Resistor Network Calculations

The SPV1050 relies on two highly specific resistor voltage dividers. Because this is an ultra-low power harvesting application, all resistor networks must have a total resistance in the megaohm ($M\Omega$) range to prevent parasitic battery drain. 1% tolerance resistors are highly recommended to ensure switching thresholds remain safe for both the LiFePO4 cell and the ESP32.

> **Battery Protection (UVP & EOC):** The internal pass transistor is controlled by a voltage divider (R4, R5, R6) to protect the LiFePO4 battery from deep discharge and the microcontroller from overvoltage. The total resistance must be between $10\ M\Omega$ and $20\ M\Omega$.
>
> To safely power the ESP32 (which has an absolute maximum rating of $3.6\ V$) while factoring in a $\pm 3\%$ worst-case tolerance stack, we target a nominal End-of-Charge ($V_{EOC}$) of $3.46\ V$ and an Undervoltage Protection ($V_{UVP}$) of $2.52\ V$.
>
> **R4:** $6.2\ M\Omega$ | **R5:** $1.6\ M\Omega$ | **R6:** $4.3\ M\Omega$ *(Total:* $12.1\ M\Omega$*)*
>
> $V_{EOC} = 1.23\ V \times (12.1\ M\Omega / 4.3\ M\Omega) = 3.46\ V$
>
> $V_{UVP} = 1.23\ V \times (12.1\ M\Omega / 5.9\ M\Omega) = 2.52\ V$

By targeting $3.46\ V$, even if all components drift to their maximum positive error limits, the peak voltage will safely cap at $\approx 3.56\ V$.

> **MPPT Configuration:** To extract maximum power from a $6.0\ V$ solar panel with a $75\%$ Maximum Power Point ratio, we use a second divider (R1, R2, R3). The target maximum MPP voltage ($V_{MPP(MAX)}$) must be configured to sit at least $100\ mV$ below the $V_{UVP}$ threshold, so we target $2.4\ V$.
>
> **R1:** $6.2\ M\Omega$ | **R2:** $1.0\ M\Omega$ | **R3:** $3.0\ M\Omega$ *(Total:* $10.2\ M\Omega$*)*
>
> $R1 = 10.2\ M\Omega \times [1 - (2.4\ V / 6.0\ V)] \approx 6.2\ M\Omega$
>
> $R2 = 10.2\ M\Omega \times (2.4\ V / 6.0\ V) \times (1 - 0.75) \approx 1.0\ M\Omega$
>
> $R3 = 10.2\ M\Omega \times (2.4\ V / 6.0\ V) \times 0.75 \approx 3.0\ M\Omega$
