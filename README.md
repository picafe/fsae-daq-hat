# FSAE Pi HAT
## What this project is

This project is a revision 2.5 of the Raspberry Pi DAQ (data acquisition) HAT for FSAE. It consolidates the features still needed from the failed rev 2 boards into a single HAT: a 24 V → 5 V buck converter to power the Pi, input protection on the car rail, CAN transceiver + controller, static PoE injection control, and RTC with coin cell for the Pi 4B.

## Why I made it

A few months ago, I tested and diagnosed a cause of catastrophic failure of the rev 2 DAQ HAT using voltage injection and a thermal camera. Although rev 3 was planned, and I am still working on it, along with others, the current failed rev 2 PCBs are still being partially used for their features that still function. This aims to be a quick revision that consolidates some of the features, while being cheap to manufacture.

The buck converter is one of the essential parts of the hat. Existing modules cut too many corners to be reliable, especially at 3A peak current. Currently, the buck coverter on the Rev 2 board is blown up. 

Rev 2 also included a boost converter to boost 20V USB-C PD to 24 V for injecting PoE for the base station antenna, and for ease of powering during testing. Since this is at a lower current, I decided to use a modified MT3608 boost converter and a PDC004 USB-C PD trigger on a separate PCB for ease of design and cost.

## Pictures

### 3D

![3D view](./images/final3d.png)

### Schematic

![Final schematic sheet](./images/pihatschfinal.png)

**[View schematic online (KiCanvas)](https://kicanvas.org/?repo=https%3A%2F%2Fgithub.com%2Fpicafe%2Ffsae-daq-hat%2Fblob%2Fmain%2Ffsae-pihat%2Ffsae-pihat.kicad_sch)**

### PCB (KiCad)

![PCB layout top layer](./images/kicad_jO8bUh94b5.png)
![PCB layout bottom layer](./images/kicad_tM0wWa0Ygl.png)

**[View PCB online (KiCanvas)](https://kicanvas.org/?repo=https%3A%2F%2Fgithub.com%2Fpicafe%2Ffsae-daq-hat%2Fblob%2Fmain%2Ffsae-pihat%2Ffsae-pihat.kicad_pcb)**

### Wiring (off-board)

The 20 V USB-C PD → 24 V boost path is **not** on this HAT. It is implemented on the companion [USB boost/PD module](./fsae-pihat-usbmod/README.md), wired to the **J3** pads on the HAT.

```text
HAT J3 pads (no header installed)
    │  wire
    ▼
Deutsch connector
    │  wire
    ▼
USB-C PD breakout (MT3608 + PDC004)
```
The 2 main power devices source power from different sources using power OR-ing with schottky barrier diodes. 

The buck converter is powered by 24V from the car rail or the 20V USB-C PD (to reduce strain on the boost converter and to keep less ripple on the 5 V rail).
The PoE injector is powered by 24V from the car rail or the 24v boost converter.

A jumper on the **24 V car** input (JP2) can disable the car rail without unplugging the Deutsch connector, to preserve the car battery during bench testing. This MUST be populated to enable the car rail with a high current 0.1" pitch jumper. There is also a CAN termination jumper (JP1) to enable or disable 120Ω termination on the CAN bus.

![Car-rail disable jumper (JP2)](./images/kicad_KYh1ov1HSV.png)

## USB boost / PD module

The MT3608 boost and PDC004 USB-C PD trigger are on a separate PCB. Schematics, layout, images, and BOM for that board are in **[fsae-pihat-usbmod/README.md](./fsae-pihat-usbmod/README.md)**.

## Bill of materials (Pi HAT)


### JLCPCB SMT assembly

| Refs | Qty | Description |
| --- | ---: | --- |
| C1, C2 | 2 | 1206 4.7 µF · [C29823](https://www.lcsc.com/product-detail/C29823.html) |
| C8, C10 | 2 | 0603 100 nF · [C14663](https://www.lcsc.com/product-detail/C14663.html) |
| C11 | 1 | 0402 1 µF · [C52923](https://www.lcsc.com/product-detail/C52923.html) |
| C3 | 1 | 0805 10 nF · [C1710](https://www.lcsc.com/product-detail/C1710.html) |
| C4 | 1 | 0402 22 pF · [C1555](https://www.lcsc.com/product-detail/C1555.html) |
| C5 | 1 | 0402 100 nF · [C307331](https://www.lcsc.com/product-detail/C307331.html) |
| C6 | 1 | D-case 330 µF 6.3 V tantalum (6TPE330MAP) · [C79112](https://www.lcsc.com/product-detail/C79112.html) |
| C7, C9 | 2 | 0402 30 pF · [C1570](https://www.lcsc.com/product-detail/C1570.html) |
| D1 | 1 | SMA 40 V TVS bidirectional (SMAJ40CA) · [C19077552](https://www.lcsc.com/product-detail/C19077552.html) |
| D10 | 1 | 0805 yellow LED · [C2296](https://www.lcsc.com/product-detail/C2296.html) |
| D11, D12, D2, D3 | 4 | SMA Schottky SS54 · [C7420369](https://www.lcsc.com/product-detail/C7420369.html) |
| D4 | 1 | SOT-23 CAN TVS (TPSM24CANB-02HTG) · [C970029](https://www.lcsc.com/product-detail/C970029.html) |
| D5 | 1 | SMC Schottky SS54C · [C18199188](https://www.lcsc.com/product-detail/C18199188.html) |
| D6, D7 | 2 | SOD-323 Schottky RB751V-40 · [C7502691](https://www.lcsc.com/product-detail/C7502691.html) |
| D8 | 1 | 0805 green LED · [C2297](https://www.lcsc.com/product-detail/C2297.html) |
| D9 | 1 | 0805 red LED · [C84256](https://www.lcsc.com/product-detail/C84256.html) |
| F1 | 1 | 1812 PTC polyfuse 1812L200/30GR · [C18198342](https://www.lcsc.com/product-detail/C18198342.html) |
| L1 | 1 | 12.5×12.5 mm 15 µH inductor SRR1260-150M · [C2041333](https://www.lcsc.com/product-detail/C2041333.html) |
| R1, R2, R14, R16, R19, R20 | 6 | 0402 470 Ω · [C25117](https://www.lcsc.com/product-detail/C25117.html) |
| R3, R9, R10, R17 | 4 | 0402 10 kΩ · [C25744](https://www.lcsc.com/product-detail/C25744.html) |
| R11 | 1 | 0402 1 kΩ · [C11702](https://www.lcsc.com/product-detail/C11702.html) |
| R7, R8, R12 | 3 | 0402 4.7 kΩ · [C25900](https://www.lcsc.com/product-detail/C25900.html) |
| R13 | 1 | 0402 680 Ω · [C25130](https://www.lcsc.com/product-detail/C25130.html) |
| R15 | 1 | 0603 120 Ω (CAN termination) · [C22787](https://www.lcsc.com/product-detail/C22787.html) |
| R18 | 1 | 0603 1 kΩ · [C21190](https://www.lcsc.com/product-detail/C21190.html) |
| R4 | 1 | 0603 3 kΩ · [C4211](https://www.lcsc.com/product-detail/C4211.html) |
| R5 | 1 | 0603 150 Ω · [C22808](https://www.lcsc.com/product-detail/C22808.html) |
| R6 | 1 | 0603×4 1 kΩ resistor network · [C20197](https://www.lcsc.com/product-detail/C20197.html) |
| S1 | 1 | Tactile switch 160 gf · [C720477](https://www.lcsc.com/product-detail/C720477.html) |
| U1 | 1 | TPS5430DDAR buck (ESOP-8) · [C9864](https://www.lcsc.com/product-detail/C9864.html) |
| U2 | 1 | PCF8563T RTC (SOIC-8) · [C7440](https://www.lcsc.com/product-detail/C7440.html) |
| U3 | 1 | MCP2515-I/ST CAN controller (TSSOP-20) · [C185941](https://www.lcsc.com/product-detail/C185941.html) |
| U4 | 1 | SN65HVD230DR CAN transceiver (SOP-8) · [C46596299](https://www.lcsc.com/product-detail/C46596299.html) |
| U5 | 1 | TPS1H200AQDGNRQ1 PoE high-side switch · [C2653785](https://www.lcsc.com/product-detail/C2653785.html) |
| X1 | 1 | 32.768 kHz ceramic resonator · [C32346](https://www.lcsc.com/product-detail/C32346.html) |
| X2 | 1 | 12 MHz crystal 3225 · [C9002](https://www.lcsc.com/product-detail/C9002.html) |


### DigiKey (hand assembly)

| Refs | Qty | Description | DigiKey | Mfr P/N |
| --- | ---: | --- | --- | --- |
| J2 | 1 | 2×20 extended female GPIO header | [1568-16763-ND](https://www.digikey.ca/en/products/detail/sparkfun-electronics/16763/12686348) | 16763 |
| J4, J5 | 2 | 4-pos 3.5 mm top-entry terminal block | [A123850-ND](https://www.digikey.ca/en/products/detail/te-connectivity-amp-connectors/1-2834011-4/5872966) | 1-2834011-4 |
| ETH1, ETH2 | 2 | RJ45 magjack 10/100 + PoE (Taoglas) | [931-TMJUTHQ0021192425-ND](https://www.digikey.ca/en/products/detail/taoglas-limited/TMJUTHQ0021192425/21777733) | TMJUTHQ0021192425 |
| FH1, FH2 | 2 | Blade fuse holder 30 A 500 V PCB | [36-3568-ND](https://www.digikey.ca/en/products/detail/keystone-electronics/3568/2137306) | 3568 |
| BT1 | 1 | CR1225 coin-cell holder, 12 mm SMD | [BAT-HLD-012-SMTCT-ND](https://www.digikey.ca/en/products/detail/te-connectivity-linx/BAT-HLD-012-SMT-TR/5361776) | BAT-HLD-012-SMT-TR |
| — | 1 | CR1225 3 V lithium cell (fits BT1) | *(Amazon, etc.)* | CR1225 |
| JP1, JP2 | 2 | 1×3 2.54 mm vertical pin header (solder to `JP1` / `JP2` footprints) | *(any 1×3 0.1″ header)* | — |
| JP1 | 1 | CAN 120 Ω termination shunt | [S9341-ND](https://www.digikey.ca/en/products/detail/sullins-connector-solutions/NPC02SXON-RC/2618266) | NPC02SXON-RC |
| JP2 | 1 | 24 V car-rail enable shunt | [S9341-ND](https://www.digikey.ca/en/products/detail/sullins-connector-solutions/NPC02SXON-RC/2618266) | NPC02SXON-RC |

**Not populated (DNP):**

| Refs | Footprint on PCB | Notes |
| --- | --- | --- |
| J1 | 2×2 pin socket | Do not install; car input harness wires solder to |
| J3 | 1×3 pin header | Do not install; boost/PD harness wires solder to |


