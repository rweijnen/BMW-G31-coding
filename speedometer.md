# BMW Speedometer Coding Guide

## Overview

This guide documents how BMW coding works for customizing speedometer and tachometer ranges across different vehicle variants. All modifications are made to the **dkombi2 module** (Digital Kombi 2 - the digital instrument cluster module) in e-Sys.

**Tested on:** G series chassis (G31) with 6WB digital speedometer/tachometer cluster

## dkombi2 Module

The dkombi2 module controls all digital gauge functionality including:
- Speedometer display and needle movement
- Tachometer display and needle movement
- Speed and RPM calibration points
- Gauge scale limits and visual design

All coding changes in this guide are applied directly to parameters within this module.

## Two Things Need to Change

To enable M Sport speedometer functionality (330 km/h display with needle movement beyond 263 km/h), **two separate modifications** must be made within dkombi2:

1. **Change the gauge scale** - Update the visual speedometer scale from 260 km/h to 330 km/h
2. **Enable needle movement beyond the standard limit** - Modify the calibration parameters that control actual needle movement

## How It Works

The speedometer and tachometer coding parameters use pairs of hexadecimal digits to define scale points. Each parameter contains 5 values (10 hexadecimal digits total) that represent calibration points on the gauge.

### Understanding Calibration Point Parameters

The key to controlling needle movement is understanding how calibration parameters work. These parameters define a mathematical mapping between actual speed values and needle position on the gauge.

### Decoding Example

Taking `GESCHWINDIGKEIT_ANZ_X` (Speedometer Display) for BMW as an example:

**Hex values:** `00, 00, 01, FE, 03, F2, 05, E6, 0A, 46`

**Grouped in pairs:**
- `0000` = 0 km/h
- `01FE` = 510 (÷10 = 51 km/h)
- `03F2` = 1010 (÷10 = 101 km/h)
- `05E6` = 1510 (÷10 = 151 km/h)
- `0A46` = 2630 (÷10 = **263 km/h** ← Maximum speed limit)

The final value represents the maximum speed displayed on the speedometer. Converting `0A46` from hexadecimal to decimal (2630) and dividing by 10 reveals the 263 km/h limit.

## Coding Parameters

### Speed-Related Parameters

#### GESCHWINDIGKEIT_EFF_X (Actual Speed)
Controls the actual speed reading input range:

| Variant | Max Speed | Calibration Points (km/h) |
|---------|-----------|---------------------------|
| BMW | 262 | 0, 50, 100, 150, 262 |
| BMW Basis | 262 | 0, 50, 100, 150, 262 |
| M Sport | 332 | 0, 50, 100, 200, 332 |
| Alpina | 340 | 0, 10, 100, 300, 340 |
| MPA N74 | 330 | 0, 50, 100, 150, 330 |

#### GESCHWINDIGKEIT_ANZ_X (Speedometer Display)
Controls the speedometer gauge display range:

| Variant | Max Speed | Calibration Points (km/h) |
|---------|-----------|---------------------------|
| BMW | 263 | 0, 51, 101, 151, 263 |
| BMW Basis | 262 | 0, 50, 100, 150, 262 |
| M Sport | 333 | 0, 51, 101, 201, 333 |
| Alpina | 350 | 0, 11.3, 101.3, 310, 350 |
| MPA N74 | 332 | 0, 51, 101, 151, 332 |

### RPM-Related Parameters

#### DZM_EFF_X (Actual RPM)
Controls the actual RPM input range:

| Fuel Type | Variant | Max RPM | Calibration Points (RPM) |
|-----------|---------|---------|--------------------------|
| Gasoline | BMW/Alpina | 7500 | 0, 2000, 4000, 6000, 7500 |
| Gasoline | M Sport | 8000 | 0, 2000, 4000, 6000, 8000 |
| Diesel | BMW/Alpina | 6000 | 0, 2000, 4000, 5000, 6000 |

#### DZM_ANZ_Y (Tachometer Display)
Controls the tachometer gauge display range:

| Fuel Type | Variant | Max RPM | Calibration Points (RPM) |
|-----------|---------|---------|--------------------------|
| Gasoline | M Sport | 8000 | 0, 2000, 4000, 6000, 8000 |
| Diesel | BMW/Alpina | 6000 | 0, 2000, 4000, 5000, 6000 |

### ACC Parameters

#### ACC_EFF_X (Actual Value)
Adaptive cruise control speed input:

| Unit | Max Value | Calibration Points |
|------|-----------|-------------------|
| km/h, mph | 262 | 0, 50, 100, 150, 262 |

#### ACC_ANZ_X (Display Value)
Adaptive cruise control speed display:

| Unit | Max Value | Calibration Points |
|------|-----------|-------------------|
| km/h, mph | 263 | 0, 51, 101, 151, 263 |

## Key Findings

1. **Non-linear calibration**: The calibration points are not equally spaced, allowing for better gauge accuracy across different speed ranges.

2. **EFF vs. ANZ distinction**:
   - **EFF (Effective)**: Handles actual sensor input values
   - **ANZ (Anzeige/Display)**: Controls visual gauge representation

3. **Variant-specific scaling**:
   - Standard BMW and Basis models typically max out around 260 km/h
   - M Sport variants support higher speeds (330+ km/h)
   - Alpina variants feature the highest limits (340+ km/h)

4. **Fuel type differences**: Diesel variants have lower RPM limits (6000) compared to gasoline engines (7500-8000).

## Practical Applications

This coding information enables:
- Customizing speedometer limits for different engine variants
- Swapping gauge clusters between vehicle configurations
- Tailoring performance parameters to specific market regulations

## Reverse Engineering Your Current Coding

To determine what variant your vehicle is currently coded as, or what changes you need to make:

1. **Read your current e-Sys values** for the parameters you want to modify
2. **Compare them against the tables above** to identify which variant they match
3. **Select your target variant** from the same parameter table
4. **Apply the new hex values** to change to your desired configuration

### Example: Upgrading Speedometer to M Sport

If your current `GESCHWINDIGKEIT_ANZ_Y` values are:
- `00, 00, 01, FE, 03, F2, 05, E6, 0A, 46` (BMW standard, 263 km/h max)

And you want to upgrade to M Sport capability:
- Change to: `00, 00, 01, FE, 03, F2, 07, DA, 0D, 02` (M Sport, 333 km/h max)

Always apply corresponding changes to both the **EFF** (actual input) and **ANZ** (display) parameters for consistency.

## Enabling M Sport Speedometer (330 km/h)

Both modifications are made within the dkombi2 module:

### Change 1: Scale Adjustment (BimmerCode)
Use BimmerCode to enable the 330 km/h scale display:
```
dkombi2 ***8212; expert mode ***8212; 3000 > MPA_ENABLE
```

This changes the physical speedometer scale from 260 km/h to 330 km/h maximum.

**Optional:** Change color scheme to grey/white M Sport styling:
```
dkombi2 ***8212; expert mode ***8212; 3000 ***8212; MPA MSP VIEW ENABLE
```

### Change 2: Enable Needle Movement Beyond 263 km/h (e-Sys)
Modify the speedometer calibration values in e-Sys:
```
dkombi2 GESCHWINDIGKEIT_EFF_X -> to m_sport
dkombi2 GESCHWINDIGKEIT_ANZ_Y -> to m_sport
```

## How Calibration Parameters Control Needle Movement

The second change is where the actual needle movement beyond 263 km/h happens. Understanding this is crucial to knowing what you're coding.

### The Calibration Point System

Each calibration parameter defines 5 reference points that tell the instrument cluster how to map actual speed to needle position:

- **Point 0:** 0 km/h = needle at start
- **Point 1:** 50-51 km/h = needle at ~25% scale
- **Point 2:** 100-101 km/h = needle at ~38% scale  
- **Point 3:** 150-201 km/h = needle at ~60% scale
- **Point 4:** 262-333 km/h = needle at end of scale

### BMW Standard vs. M Sport

**BMW Standard (263 km/h max):**
```
GESCHWINDIGKEIT_EFF_X: 00, 00, 01, F4, 03, E8, 05, DC, 0A, 3C
GESCHWINDIGKEIT_ANZ_Y: 00, 00, 01, FE, 03, F2, 05, E6, 0A, 46
```
Final value: `0A46` = 2630 decimal ÷ 10 = **263 km/h** (maximum needle travel)

**M Sport (333 km/h max):**
```
GESCHWINDIGKEIT_EFF_X: 00, 00, 01, F4, 03, E8, 07, D0, 0C, F8
GESCHWINDIGKEIT_ANZ_Y: 00, 00, 01, FE, 03, F2, 07, DA, 0D, 02
```
Final value: `0D02` = 3330 decimal ÷ 10 = **333 km/h** (maximum needle travel)

### Why This Matters

The final calibration point value directly controls where the needle stops. By changing from `0A46` (263 km/h) to `0D02` (333 km/h), you're telling the instrument cluster to extend the needle travel distance to reach the end of the new 330 km/h scale.

Without this change, even with the scale set to 330 km/h, the needle would still max out at 263 km/h, leaving half the gauge unused.

**Why both steps are necessary:** 
- Change 1 (scale) updates the visual gauge design
- Change 2 (calibration) extends the actual needle movement range to match the new scale

The calibration values literally define how far the needle can physically move on the display.

**Disclaimer:** To actually achieve speeds beyond 263 km/h on your speedometer, you'll need both a sufficiently powerful vehicle and the speed limiter disabled. The coding simply allows the gauge to display higher speeds—it doesn't make your car faster! 😉

## References

- Original findings extracted from BMW 3003 Analogue_Zeiger module in e-Sys
