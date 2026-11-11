# Zoom Search Tool

A browser-based tool for exploring four-group zoom lenses with two moving groups. G1 and G4 remain fixed, while G2 and G3 move to maintain the requested effective focal length (EFL) and a fixed image plane. Both solutions are displayed side by side.

## Getting started

Download [Zoom Search Tool.html](Zoom%20Search%20Tool.html) and open it in a browser. No installation is required.

1. Set the EFL range, total track, rear-group-to-image distance, F-number, and image height.
2. Adjust each group's focal length using its slider or editable value. The minimum and maximum values define the slider range.
3. Select the aperture-stop group using the checkbox beside its label.
4. Move the shared zoom slider to compare both solutions at the same EFL. Results update automatically.
5. Configure the CODE V seed settings and select **Export .seq** for either solution.

You can also edit the EFL below the zoom slider to select a zoom position. Editing the shared F/# sets a constant F-number across zoom; use the short- and long-end inputs to specify a varying F-number.

## Results

Each solution includes a paraxial ray layout, group positions and separations, and plots of group motion, incident angles, and stop diameter. Blue rays represent the center field; orange rays represent the edge field.

Image height stays fixed through zoom, while the object-space field angle changes. Use approximately 10.8 mm for Micro Four Thirds or 21.6 mm for full-frame diagonal coverage.

The model assumes an object at infinity in air and represents each group as a thin lens. Crossed groups and negative separations remain visible for comparison, even when the arrangement is not physically usable.

## Algorithm

### Paraxial ray tracing

A ray is represented by its height $y$ and slope $u=\tan\theta$, where $\theta$ is measured from the optical axis. Group $i$ has signed focal length $f_i$ and power $\phi_i=1/f_i$. Propagation through a signed axial distance $d$ and refraction by a thin group use these updates:

$$
\begin{aligned}
\text{Propagation:}\quad &(y,u)\mapsto(y+du,\ u) \\
\text{Thin group:}\quad &(y,u)\mapsto(y,\ u-\phi_i y).
\end{aligned}
$$

The trace records the incoming angle $\arctan(u)\,180/\pi$ and height at each group before applying its power. After G4, it propagates the ray to the image plane by the rear distance $b$, giving $y_{\mathrm{img}}=y_4+b\,u_4^+$, where $u_4^+$ is the slope just after G4.

For target EFL $F$ and fixed edge image height $H$, the input slopes for the negative edge, center, and positive edge fields are $-H/F$, $0$, and $H/F$. The edge field angle is therefore $\arctan(H/F)$. If the first row of the ray transfer matrix from just before G1 to the stop plane is $(A_s,B_s)$, then $y_s=A_s y_0+B_s u_0$. For each field the tool launches the two marginal rays and the chief ray using

$$
r_{\mathrm{EP}}=\frac{F}{2N},\qquad
y_0=-\frac{B_s}{A_s}u_0+p\,r_{\mathrm{EP}},\qquad
p\in\{-1,0,1\}.
$$

Here $N=N_{\mathrm{short}}+t(N_{\mathrm{long}}-N_{\mathrm{short}})$ is the F-number at the current zoom fraction $t=(F-F_{\mathrm{short}})/(F_{\mathrm{long}}-F_{\mathrm{short}})$. When both EFL endpoints are equal, the trace uses $t=0$. The $p=0$ ray passes through the stop center, and the stop diameter is $2|A_s|r_{\mathrm{EP}}$. The reported group angle and ray height are the largest absolute values across the nine traced rays.

### Petzval sum

The displayed thin-group Petzval estimate is

$$
P=\sum_{i=1}^{4}\frac{\phi_i}{n_i}
 =\sum_{i=1}^{4}\frac{1}{n_i f_i}\qquad [\mathrm{mm}^{-1}].
$$

The tool uses the positive-lens index for groups with $f_i>0$ and the negative-lens index for groups with $f_i<0$. This estimate depends on the group focal lengths and the two index settings, so both solutions share it at every zoom position.

### Zoom position calculation

For a normalized slider position $t$ between 0 and 1, the target EFL is $F=F_{\mathrm{short}}+t(F_{\mathrm{long}}-F_{\mathrm{short}})$. Let $L$ be the total track and $b$ the G4-to-image distance. The fixed groups are at $z_1=0$ and $z_4=L-b$, with the image at $z=L$.

The solver traces a normalized parallel ray starting with $(y,u)=(1,0)$. To achieve EFL $F$ and focus at the fixed image plane, its height and slope just after G4 must be $b/F$ and $-1/F$. The required slope just before G4 is therefore $u_*=(\phi_4 b-1)/F$. For a trial G2 position $z_2$, the height at G2, slope after G2, and required height at G3 are

$$
y_2=1-\phi_1 z_2,\qquad
u_2=-\phi_1-\phi_2 y_2,\qquad
y_3=\frac{u_2-u_*}{\phi_3}.
$$

The two propagation relations are $y_3=y_2+(z_3-z_2)u_2$ and $b/F=y_3+(z_4-z_3)u_*$. Eliminating $z_3$ gives a quadratic residual in the normalized position $x=z_2/z_4$:

$$
Q(x)=\left( \frac{b}{F}-y_3-(z_4-z_2)u_* \right) u_2
      +u_*(y_3-y_2)=0.
$$

The code obtains the coefficients of $Q(x)=a x^2+c_1 x+c_0$ by evaluating the residual at three points:

$$
c_0=Q(0),\qquad
c_1=\frac{Q(1)-Q(-1)}{2},\qquad
a=\frac{Q(1)+Q(-1)}{2}-c_0.
$$

The discriminant is $\Delta=c_1^2-4ac_0$. For real roots, the code uses $q=-\tfrac12(c_1+s\sqrt{\Delta})$, where $s=1$ if $c_1\ge 0$ and $s=-1$ otherwise. The roots are $q/a$ and $c_0/q$, with the repeated root $-c_1/(2a)$ used when $q=0$. If $a$ is effectively zero and the linear term is nonzero, it solves $x=-c_0/c_1$.

For each real root, it recovers $z_2=xz_4$ and $z_3=z_2+(y_3-y_2)/u_2$. When $u_2$ is effectively zero, it uses the rear propagation relation $z_3=z_4-(b/F-y_3)/u_*$ if $y_3\approx y_2$ and $u_*$ is nonzero. The solver verifies each finite candidate using the ray transfer matrix through G4. Its first column $(A,C)^\mathsf{T}$ must satisfy $C=-1/F$ and $A+bC=0$ within numerical tolerance.

The motion plots use 101 evenly spaced target EFLs. The tool matches roots by their G2 and G3 positions to follow the two branches and solves again at the exact EFL selected by the slider.

## CODE V export

The export creates a starting prescription with 3, 4, or 5 singlets per group. Settings include lens thickness, positive- and negative-lens refractive indices, wavelength presets, equal-area field sampling, and the number of zoom positions.

Available wavelength presets are monochromatic, dFC (default), eFC, LWIR, and MWIR. The stop is exported as a separate plane dummy surface, and overlapping groups retain negative air thicknesses.

**Spherical correction (experimental)** numerically redistributes lens powers to reduce the mean squared primary spherical-aberration sum of the complete system across 101 zoom positions. Each group retains its target focal length, and lens thickness stays at the entered value. The search starts from two power distributions and retains the best result found.

The layout is a paraxial model, and the exported prescription is an optimization seed. Real-surface aberrations and finite lens thickness can change its behavior. Check EFL, focus, apertures, and image quality in CODE V before developing the design further.
