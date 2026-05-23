# Assembly instructions

## 1. USB-C PD / boost module (`fsae-pihat-usbmod`)

This board converts 20 V USB-C PD → 24 V for PoE injection and bench power. It is fully hand-assembled.

### Bill of materials

| Ref | Qty | Part | Notes |
| --- | ---: | --- | --- |
| J2 | 1 | PDC004 V2 USB-C PD trigger | Fixed **20 V** output; AliExpress module (HT50 regulator rev) |
| U1 | 1 | MT3608 boost module | Generic AliExpress / module; ~2 A capable |
| C1 | 1 | 2.2 µF 50 V X7R **1210** ([CL32B225KBJNNNE](https://www.digikey.ca/en/products/detail/samsung-electro-mechanics/CL32B225KBJNNNE/3888542)) | Hand-solder on MT3608 **output**; not placed on PCB |

### Board layout

- **Top:** MT3608 boost (**U1**), output header (**J1**)
- **Bottom:** PDC004 PD module (**J2**), USB-C connector faces down

Both modules use large SMD pads with through-holes, so they are soldered directly to the carrier PCB.

### Assembly order


1. **Solder J2 (PDC004) on the bottom side**
   - Flip the board so the bottom is up.
   - Align the PDC004 module to the **J2** footprint (`USB C` silkscreen marks the connector end).
   - Match **OUT+** (20 V) and **OUT−** (GND) to the labeled pads.
   - Place your soldering iron on the pad, hold for ~6 seconds, and feed solder onto the other side of the pad where your iron isn't.
   - Repeat for the second pad.
   - Turn the board around and look at the vias on the bottom side, underneath where you just soldered.
   - Check to make sure the solder is flowing into the vias and not pooling on the pad. If this is the case, you may need to reflow the pad with flux and/or more heat. 
   - (optional) Add a small piece of kapton tape on the other side of the pcb to conver the vias you just inspected to prevent shorting.

2. **Solder U1 (MT3608) on the top side**
   - Flip to the top side.
   - Align to the **U1** footprint using the silkscreen labels:
     - **VIN+** / **VIN−** input from PDC004 (+20 V / GND)
     - **VOUT+** / **VOUT−** boosted output (+24 V / GND)
   - Solder all four module pads, using the same method as the PD module.

3. **Solder C1 on the MT3608 output**
   - **C1 is not on the PCB**. Solder the 1210 cap directly across the MT3608 module **VOUT+** and **VOUT−** pads (same nodes as the module’s output pins).
   - This follows the approach from [GreatScott’s video](https://www.youtube.com/watch?v=6bicunweBAQ&t=666s) and reduces output ripple/stability issues noted in the design journal.
   - See the video for reference (timestamp linked above).


4. **Adjust boost output to 24 V**
   - Connect a USB-C **PD supply** capable of 20 V to the PDC004.
   - Verify PDC004 output is ~20 V.
   - Turn the MT3608 onboard trimpot while monitoring **VOUT+** until the output reads **~24 V** under light load.


### Test before connecting to the HAT

1. USB-C PD in → verify ~20 V at PDC004 output.
2. Verify ~24 V at J1 pin 1 (boost output).
3. Verify J1 pin 2 ≈ 20 V.
4. Confirm no excessive ripple on the 24 V output with C1 installed.

If there are any issues, it is likely the modules are faulty or DOA. Make sure to check before connecting to the HAT.

**Moreover, it is essential to use a quality PD charger, as there is little protection on these inputs. The design relies on the PD charger shutting itself off in case of a short circuit or overcurrent.**

---

## 2. Main Pi HAT (`fsae-pihat`) 

Most SMT parts are assembled at JLCPCB. The items below are hand-assembled after receiving boards.

### Hand-solder (DigiKey)

| Ref | Qty | Part |
| --- | ---: | --- |
| J2 | 1 | 2×20 extended female GPIO header (solder on **bottom**) |
| J4, J5 | 2 | 4-pos 3.5 mm top-entry terminal block |
| ETH1, ETH2 | 2 | Taoglas TMJUTHQ0021192425 RJ45 magjack (PoE) |
| FH1, FH2 | 2 | Keystone 3568 blade fuse holder + blade fuses (see silkscreen current limits: **1.5 A MAX**, **500 mA MAX**) |
| BT1 | 1 | CR1225 coin-cell holder (solder on **bottom**) |
| - | 1 | CR1225 3 V cell (insert after assembly) |
| JP1, JP2 | 2 | 1×3 2.54 mm vertical pin header |
| JP1, JP2 | 2 | Jumper shunt ([S9341-ND](https://www.digikey.ca/en/products/detail/sullins-connector-solutions/NPC02SXON-RC/2618266) or equivalent **3 A** rated) |

### Do not populate (DNP)

| Ref | Instead |
| --- | --- |
| **J1** (2×2 socket) | Solder car input / CAN harness wires directly to J1 |
| **J3** (1×3 header) | Solder USB-module harness wires directly to J3 |

### Jumpers

#### JP2 - 24 V car-rail enable

Labeled **EN** / **OFF** on silkscreen. The car rail can be disabled during bench testing without unplugging the Deutsch connector (preserves the car battery).

| Shunt position | Effect |
| --- | --- |
| **Pins 1–2** (toward **EN**) | Car 24 V rail **enabled**; required for in-car operation |
| **Removed** or on pins 2–3 (**OFF**) | Car 24 V rail **disabled**; use for bench/PD testing |

#### JP1 - CAN bus termination

| Shunt position | Effect |
| --- | --- |
| **Pins 1–2** | 120 Ω CAN termination **enabled** (use when this board is at the end of the CAN bus) |
| **Removed** or on pins 2–3 | Termination **disabled** |

### Suggested order

1. Inspect SMT work (especially buck area **U1/L1/C6** and PoE switch **U5**). Add solder on the underside of the board if there is too little on the center ground vias of the buck converter and high-side switch.
2. Solder **J2** GPIO header on the bottom.
3. Solder **BT1** on the bottom; insert CR1225 cell later.
4. Solder **ETH1**, **ETH2**, **J4**, **J5**, **FH1**, **FH2** on the top.
5. Install blade fuses in **FH1** / **FH2**.
6. Solder **JP1** and **JP2** pin headers; leave shunts off until you configure the board.
7. Set jumpers:
   - **JP2:** shunt on 1–2 for car use; remove for bench/PD-only testing.
   - **JP1:** shunt on 1–2 only if this node needs CAN termination.

### Off-board wiring summary

```text
Car Deutsch harness ──► J1 pads (24 V in, GND, CAN H/L)
USB boost module   ──► J3 pads (+24V BST, 20V USB, GND)
```
