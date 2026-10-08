# 16-Mask CMOS Process Flow

This repository documents the **step-by-step fabrication of a CMOS pair** (PMOS in an N-well and NMOS in a P-well) on a **P-substrate** using a **16-mask CMOS process**.

The focus is on understanding **what happens at each stage**, from defining the active region and wells to forming the gate, source/drain, contacts, and multi-level metal interconnects.

<img width="900" alt="Final fabricated CMOS structure" src="images/19.png" />

---

## Table of Contents

1. [Active Region Definition (Mask 1)](#1-active-region-definition-mask-1)
2. [Field Oxide (LOCOS)](#2-field-oxide-locos)
3. [N-well and P-well Formation (Mask 3)](#3-n-well-and-p-well-formation-mask-3)
4. [Threshold Voltage Reference](#4-threshold-voltage-reference)
5. [Gate Formation](#5-gate-formation)
6. [Lightly Doped Drain (LDD) Formation](#6-lightly-doped-drain-ldd-formation)
7. [Source and Drain Formation](#7-source-and-drain-formation)
8. [Contacts and Local Interconnects](#8-contacts-and-local-interconnects)
9. [Higher Level Metal Formation](#9-higher-level-metal-formation)
10. [Final Device](#10-final-device)

---

## 1. Active Region Definition (Mask 1)

Transistors are built only in selected "active" regions. The wafer gets a stack of:

- ~40 nm SiO2 (pad oxide)
- ~80 nm Si3N4 (nitride)
- ~1 µm photoresist

UV light is shone through **Mask 1**, so only selected areas of the resist are exposed. After developing, the resist protects the areas where transistors will be formed.

<img width="900" alt="Mask 1 exposure" src="images/mask1.png" />

---

## 2. Field Oxide (LOCOS)

Nitride that is not protected by resist is etched away. The wafer is then oxidised, and thick **field oxide** grows only where there is no nitride. This isolates the transistors from each other.

- The process is called **LOCOS** (Local Oxidation of Silicon).
- The oxide grows slightly under the nitride edges, known as the **bird's beak**.

<img width="900" alt="Field oxide and bird's beak" src="images/2.png" />

---

## 3. N-well and P-well Formation (Mask 3)

Wells let both transistor types sit on one wafer: PMOS needs an n-type body (**N-well**) and NMOS needs a p-type body (**P-well**). Photoresist patterned with **Mask 3** blocks the dopants from regions where the well is not wanted, and the implant forms the wells in the substrate.

<img width="900" alt="N-well and P-well formation" src="images/3.png" />

---

## 4. Threshold Voltage Reference

`Vt = Vto + γ ( √|−2Φf + Vsb| − √|−2Φf| )`

| Term | Meaning |
|------|---------|
| **Vto** | Threshold voltage at Vsb = 0 (depends on the manufacturing process) |
| **γ** | Body effect coefficient |
| **Φf** | Fermi potential |
| **Vsb** | Source-to-body voltage. Increasing it increases Vt (body effect) |

When Vgs reaches Vt, the surface under the gate inverts to n-type and a channel forms between source and drain.

<img width="900" alt="Threshold voltage equation" src="images/4.png" />

---

## 5. Gate Formation

**Gate oxide:** the original oxide is stripped using dilute **HF**, then a fresh, high quality oxide (~10 nm) is grown. A thin layer in the channel region is doped (N / P marked in the figure) to set the threshold voltage.

<img width="900" alt="Gate oxide regrowth" src="images/5.png" />

**Gate patterning (Mask 6):** polysilicon is deposited over the oxide, resist is applied, and **Mask 6** defines the gate. The poly is etched so only the gates remain.

<img width="900" alt="Gate patterning with Mask 6" src="images/6.png" />

---

## 6. Lightly Doped Drain (LDD) Formation

A light implant is done on both sides of each gate:

- **P-** implant in the N-well (PMOS)
- **N-** implant in the P-well (NMOS)

Resist covers the other device each time. The gate blocks the implant, so the regions align with the gate edge (self-aligned). LDD reduces the electric field at the drain edge.

<img width="900" alt="LDD implant" src="images/7.png" />

---

## 7. Source and Drain Formation

**Spacers** (green) are formed on the sidewalls of the gate. A heavy implant then creates:

- **P+** source/drain in the N-well
- **N+** source/drain in the P-well

The spacer keeps the LDD region next to the channel lightly doped.

<img width="900" alt="Source and drain implant" src="images/8.png" />

---

## 8. Contacts and Local Interconnects

A titanium based layer is formed on the source, drain and gate top (dark blue) to give low resistance contacts. The unwanted **TiN** is etched away using **RCA cleaning**, so the contact layer stays only where needed.

<img width="900" alt="Contact formation and RCA cleaning" src="images/9.png" />

---

## 9. Higher Level Metal Formation

### 9.1 Insulating layer

~1 µm of SiO2 doped with phosphorus or boron (phosphosilicate glass / borophosphosilicate glass) is deposited over the whole wafer to insulate the transistors from the metal above.

<img width="900" alt="PSG / BPSG deposition" src="images/10.png" />

### 9.2 Planarization

**CMP** (Chemical Mechanical Polishing) flattens the wafer surface so the next layers can be patterned properly.

<img width="900" alt="CMP planarization" src="images/11.png" />

### 9.3 Contact plugs

Contact holes are opened down to the source, drain and gate, lined with a thin layer (pink) and filled with metal plugs (blue). **CMP** is done again to remove the extra metal and flatten the surface.

<img width="900" alt="Contact plugs" src="images/12.png" />

### 9.4 Metal 1 deposition

An **aluminum (Al)** layer is deposited on top of the flat surface.

<img width="900" alt="Aluminum deposition" src="images/13.png" />

### 9.5 Metal 1 patterning

The Al is **plasma etched** using a patterned resist, leaving metal lines that connect to the plugs below.

<img width="900" alt="Metal 1 plasma etch" src="images/14.png" />

### 9.6 Inter-metal oxide

**SiO2** is deposited over the metal lines and polished flat with **CMP**.

<img width="900" alt="SiO2 deposition and CMP" src="images/15.png" />

### 9.7 Vias

Holes (vias) are etched down to Metal 1 and **TiN** is deposited as the liner inside them.

<img width="900" alt="Via formation and TiN" src="images/16.png" />

### 9.8 Metal 2 (Mask 15)

The vias are filled with metal plugs and another Al layer is deposited. **Mask 15** defines the Metal 2 pattern.

<img width="900" alt="Metal 2 with Mask 15" src="images/17.png" />

### 9.9 Top protection layer

A top dielectric layer of **Si3N4** is deposited to protect the chip.

<img width="900" alt="Top dielectric Si3N4" src="images/18.png" />

---

## 10. Final Device

The metal layers bring out the **Source (S), Gate (G) and Drain (D)** terminals of both transistors. This completes the fabrication of the CMOS pair.

<img width="900" alt="Final device with S, G, D terminals" src="images/19.png" />

---

## Keywords

`LOCOS` `bird's beak` `N-well` `P-well` `gate oxide` `polysilicon` `LDD` `spacer` `source/drain implant` `silicide` `CMP` `plug` `via` `passivation`
