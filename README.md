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

## CODE V export

The export creates a starting prescription with 3, 4, or 5 singlets per group. Settings include lens thickness, positive- and negative-lens refractive indices, wavelength presets, equal-area field sampling, and the number of zoom positions.

Available wavelength presets are monochromatic, dFC (default), eFC, LWIR, and MWIR. The stop is exported as a separate plane dummy surface, and overlapping groups retain negative air thicknesses.

**Spherical correction (experimental)** adjusts the lens powers within each group to reduce primary spherical aberration. It requires zero lens thickness and treats each group separately.

The layout is a paraxial model, and the exported prescription is an optimization seed. Real-surface aberrations and finite lens thickness can change its behavior. Check EFL, focus, apertures, and image quality in CODE V before developing the design further.
