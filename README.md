# Oriceps Fold Simulator

An interactive, single-file web tool that shows how the **Oriceps** origami forceps fold.
Change one fold angle (or the geometry of the crease pattern) and see, in 3D and in a chart,
how far the jaws close.

Built as teaching material for an *Unconventional Robotics* class. It is meant to give students a feel for
rigid-foldable origami, degree-4 vertices and compliant mechanisms before they print or cut their own.

> **Original design credit**
> The crease pattern simulated here is the **Oriceps** design by the
> **Compliant Mechanisms Research Group (CMR), Brigham Young University**.
> This repository contains an independent simulation of that pattern. It is not affiliated with or endorsed by BYU CMR.
> See [Source and attribution](#source-and-attribution) below.

---

## Quick start

1. Download `oriceps-fold-sim-en.html`.
2. Open it in any modern browser (Chrome, Edge, Firefox or Safari). No server or install is needed.

An internet connection is required on first load, because the page loads
[three.js r128](https://threejs.org/) and Google Fonts from public CDNs.

## What you can do

| Control | Effect |
|---|---|
| **Input fold angle**: *Center b*, *Side c* or *Tip gap* | Pick which quantity the slider drives. The rest of the mechanism follows, since the pattern has one degree of freedom. |
| **Fold / unfold** | Animates the full folding range. |
| **Stop when tips touch** | Limits the motion to the point where the two jaw tips meet. |
| **Angle between d and b (θd)** | Changes the slope of diagonal crease d by changing the flap-end width. Grey curves in the chart show other angles for comparison. |
| **Pattern dimensions** | Center rectangle width and height, flap-end width and height, and overall length, all in mm. **Reset to original** restores the BYU handout dimensions. |
| **3D view** | Drag to rotate, scroll to zoom. |

**Readouts**

- Jaw tip gap (mm)
- Angle between the jaws (180° when flat)
- Handle width, measured between the top and bottom flap edges
- Fold angles of creases a, b, c and d

## The pattern

```
        top flap (T)
   ______________________
  /   |              |   \          b  : mountain (red, solid)
 / LU | d          d | RU \         a, c, d : valley (blue, dashed)
/_____|______b_______|_____\
|  a  |              |  a  |
| LJ  c      C       c  RJ |        C  : center rectangle
|_____|______________|_____|        LJ, RJ : jaws
\  a  |      b       |  a  /        T, Bm : top / bottom flaps (handles)
 \ LL | d          d | RL /         LU, LL, RU, RL : side triangles
  \___|______________|___/
        bottom flap (Bm)
```

Default dimensions are taken from the BYU CMR paper handout (Letter page, first copy of the pattern; 1 pt = 0.3528 mm):

| Dimension | Value |
|---|---|
| Center rectangle (2·x<sub>c</sub> × 2·y<sub>c</sub>) | 43.96 × 19.54 mm |
| Flap end width 2·x<sub>t</sub> | 24.43 mm |
| Flap end height 2·y<sub>t</sub> | 58.62 mm |
| Overall length 2·L | 190.50 mm |
| Angle between d and b, θ<sub>d</sub> | 63.4° |

## Model

Each corner of the center rectangle is a **degree-4 vertex**, where creases a, b, c and d meet.
The center panel is held fixed. Panels are rigid and zero-thickness, and creases are ideal hinges.

At the top-left vertex, the four sector angles going counter-clockwise from b are:

| Sector | Panel | Angle |
|---|---|---|
| b → d | T | θ<sub>d</sub> |
| d → a | LU | 180° − θ<sub>d</sub> |
| a → c | LJ | 90° |
| c → b | C | 90° |

The side triangle LU must stay rigid while the top flap T rotates about b and the jaw LJ rotates about c.
That condition gives a closed-form relation between the two fold angles:

$$\tan\frac{\rho_c}{2} = \tan\theta_d \cdot \sin\lvert\rho_b\rvert$$

- ρ<sub>b</sub> is the fold angle of the mountain crease b.
- ρ<sub>c</sub> is the fold angle of the valley crease c, which carries the jaw.

The pattern is symmetric left–right and top–bottom, so this single relation drives the whole mechanism.
Folds d and a are then recovered from the panel normals. The jaw tips are the mid-points of the jaw end edges, so

$$\text{tip gap} = 2\,\bigl(x_c + (L - x_c)\cos\rho_c\bigr)$$

Consequences you can explore with the tool:

- **Maximum c fold.** ρ<sub>c</sub> peaks at ρ<sub>b</sub> = 90°, where ρ<sub>c,max</sub> = 2θ<sub>d</sub> (126.9° for the original pattern).
- **When the tips touch.** They meet when cos ρ<sub>c</sub> = −x<sub>c</sub>/(L − x<sub>c</sub>). For the original pattern that is ρ<sub>b</sub> ≈ 43° and ρ<sub>c</sub> ≈ 107.5°.
- **Minimum θ<sub>d</sub> for full closure.** The jaws can only close fully if θ<sub>d</sub> is above about 53.7° (original center width and length). A steeper d closes the jaws with less input, but makes them more sensitive.

The relation was checked numerically. Assembling all nine panels from it preserves every flat edge length to machine precision.

## Limitations

- This is an ideal rigid-origami model. Panel thickness, hinge stiffness and compliance are ignored,
  so a 3D-printed or thick-material Oriceps closes slightly less than shown.
- There is no collision detection beyond the jaw-tip contact check.
- Only the symmetric folding mode is modelled, which is the one used by the forceps.

## Files

| File | Description |
|---|---|
| `oriceps-fold-sim-en.html` | The simulator. A single self-contained page apart from the CDN-loaded three.js and fonts. |
| `README.md` | This file. |

## Source and attribution

**Original design: Oriceps (Origami-Inspired Forceps)**
- Designers: Compliant Mechanisms Research Group (CMR), Brigham Young University. CMR's design was inspired by Shafer's origami "Chomper".
- Thingiverse: [*Oriceps: Origami Inspired Forceps* by BYU_CMR, thing:2988740](https://www.thingiverse.com/thing:2988740), published 26 July 2018.
- Paper handout used for the default dimensions and crease assignment: *Oriceps Paper Handout* (BYU CMR), distributed with the Thingiverse files and on the [BYU CMR website](https://compliantmechanisms.byu.edu/).
- License of the original files: **Creative Commons Attribution–NonCommercial (CC BY-NC)**, as stated on the Thingiverse page. Commercial licensing of the technology goes through the BYU Technology Transfer Office.

**Technical reference**
- B. J. Edmondson, L. A. Bowen, C. L. Grames, S. P. Magleby, L. L. Howell, T. C. Bateman,
  "Oriceps: Origami-Inspired Forceps," *Proc. ASME 2013 Conference on Smart Materials, Adaptive Structures and Intelligent Systems* (SMASIS2013-3299), Snowbird, UT, 2013.

**This repository** re-implements the folding kinematics of that published crease pattern for teaching.
No BYU CMR files (STL, DXF or PDF) are redistributed here. Because the default geometry is derived from the CC BY-NC handout,
**please keep uses of this simulator non-commercial** and keep the attribution above when sharing or adapting it.

**Third-party libraries** (loaded from CDN, not bundled)
- [three.js](https://github.com/mrdoob/three.js) r128 and its OrbitControls, MIT License
- Fonts: Space Grotesk, Inter and JetBrains Mono via Google Fonts, SIL Open Font License

## License

Simulator code: choose a license for your repository. MIT is a common choice for the code itself.
Whatever you choose, the attribution and non-commercial note for the original Oriceps design above still apply.
