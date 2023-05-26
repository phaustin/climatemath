---
jupytext:
  cell_metadata_filter: -all
  notebook_metadata_filter: -all
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.14.5
kernelspec:
  display_name: Python 3 (ipykernel)
  language: python
  name: python3
---

```{eval-rst}
.. default-role:: math
```

(week2-flux-from-radianceii)=

# Flux and radiance review

- Question:  "I'm confused about the difference between emission/radiance/flux. The incoming flux = irradiance = E. Radiance = L = independent of distance = luminosity. What's the outgoing flux then? Is the emission the outgoing flux? In Q4 of Beer's Law in MT Questions Part 1, it asks us to calculate emission (among other things). Are we calculating the outgoing flux (aka irradiance)? Or the outgoing radiance?

- The pertinent review question is {ref}`mid_review1` Q4:

- "Find the narrow beam transmission, absorption and emission for a series of
  stacked layers of equal transmissivities and temperatures in a direction perpendicular
  to the layers"

  By restricting the situation to the "narrow beam approximation" reviewed below
  we get to use the fact that `E = L \Delta \omega`, so that the relationship between
  flux and radiance in as simple as possible.  After the midterm we'll talk about
  how to handle the more general situation where photons are coming from all
  directions.  This requires a new idea called the "diffuse transmissivity" that
  we haven't had yet (and that's a bit of a detour from remote sensing, where the
  "narrow beam approximation" almost always works.

- Big question: climate models need fluxes to calculate how the atmosphere is heating and cooling.  Satellites measure radiances, not fluxes.  How do we get from flux to radiance for a real atmosphere?

## Definitions

- First some definitions -- see [Wikipedia](https://en.wikipedia.org/wiki/Radiometry)
  for the (really complicated) full story.

  1. **Spectral (monochromatic) radiative flux density (or monochromatic irradiance). Symbol** `E_\lambda`: the energy of photons of a given wavelength crossing a plane surface from all directions.  We need to know this when we try to figure out whether a horizontal layer of the atmosphere is being heated or cooled by radiation.  The direction of the flux is defined as being perpendicular to the plane that it is crossing.  Monochromatic (single wavelength) Units: `W\,m^{-2}\,\mu m^{-1}`

  2. **Spectral (monochromatic) radiance (also sometimes called intensity). Symbol** `L_\lambda`: the energy of photons of wavelength `\lambda` traveling within a solid angle (cone) field of view and crossing a surface. This is much more common than flux in remote sensing measurements, because every instrument has some limited field of view. The radiance direction is defined by the direction of the "spine" of the cone field of view.  Monochromatic units: `W\,m^{-2}\,\mu m^{-1}\,sr^{-1}`

  3. **Slant path. Symbol s.**  The distance traveled along the cone field of view.
     From the figure below:  `z = s/\cos \theta`. Units: m.

     ```{image} ./figures/ppt_slant_path.png
     :width: 40.0%
     ```

  4. **Solid angle. Symbol** `\omega`.  Definition from the {ref}`radiance` notes.

     $$
     d\omega = \frac{dA}{r^2} =\frac{ width \times height}{r^2} =  \sin \theta \times  d\phi \times d\theta =  \sin \theta d\theta d \phi
     $$ (domega)

## Relationship between flux and radiance

### Exact equation -- always correct

To get the flux `E` from the radiance `L` in any situation, it always works to integrate {eq}`fluxrel` from the {ref}`week2_flux_from_radiance` notes:

$$
E =  \int_0^{2\pi}\int_0^{\pi/2} L \cos \theta  \sin \theta \, d\theta \, d \phi =
     \int_0^{2\pi}\int_0^{1}\, \mu \,L\,  d \mu d\phi =        \int_0^{2\pi}\int_0^{1} \mu L  d \omega
$$ (flux_revB)

where we've used the change of variable `\mu = \cos \theta` with `d\mu = -\sin \theta d\theta` as discussed in class so we can forget about trig integrals.

We've covered two important approximations that make this integration trivial -- narrow beam
radiation and isotropic radiation.  They are summarized below.

### Narrow beam approximation -- flux at a satellite

For remote sensing, we can use a simple approximation to do this integral, because the satellite telescope restricts the solid angle we're sampling to a very thin cone pointed straight away from
the sensor.  The fact that the telescope is perpendicular to the camera means that the zenith angle
within the cone, `\theta \approx 0` almost exactly,
so when we integrate over the cone we can take
`cos\,\theta = \mu = 1`.  It also means that `L` is very nearly
constant within the cone, so we can move it out of the integral and get:

$$
E_{satellite} =  \int_0^{2\pi}\int_0^{\pi/2} L \cos \theta  \sin \theta \, d\theta \, d \phi =
     L \int_{\Delta \omega}   d \omega = L \Delta \omega
$$ (flux_revC)

### Narrow beam approximation -- flux from the sun

The other case where we can use the narrow beam approximation is for direct sunlight, because
at Earth's orbit the sun subtends a solid angle of only about `1 \times 10^{-4}` sr.  That means
we have the same situation as with the telescope, and we can write {eq}`flux_revC` as:

$$
E_{sun} = L_{sun} \Delta \omega
$$ (Elambda2)

Again, this works because **100% of the the flux falls into the narrow field of view** `\Delta \omega`, so that `L` is zero at all other solid angles outside of `\Delta \omega`

**Some numbers**: Specifically, we know that the sun has a radius of about `7 \times 10^ 8\ m`, and is about
`150 \times 10^9\ m` away from the earth.  This means that, to an excellent approximation,

$$
\Delta \omega_{sun} = \frac{A}{r^2} = \frac{\pi (7 \times 10^8)^2}{(150 \times 10^9)^2} = 7 \times 10^{-5}\ sr
$$

We also know that the sun has a surface temperature of 5780 K and is radiating like a blackbody, so:

$$
L_{sun} = \frac{\sigma}{\pi} 5780^4 \approx 20 \times 10^6\ W\,m^{-2}\,sr^{-1}
$$

Put those together and we get:

$$
E_{sun} = L_{sun} \Delta \omega_{sun} \approx 1400\ W\,m^{-2}
$$

This is the situation Stull is illustrating in Figure 2.2 -- sunlight at noon:

```{image} ./figures/stull_fig2_2_direct.png
:width: 40.0%
```

### Narrow beam approximation -- sun at zenith angle `\theta_{0}`

If it isn't noon, the sun isn't directly overhead and the solar zenith angle `\theta_0 \ne 0`, so we've got the
situation discussed in the {ref}`week2_flux_from_radiance` notes {eq}`cosflux`:

$$
E = \cos \theta_0  L  \Delta \omega
$$ (cosflux2)

Note that the zenith angle of the sun `\theta_0` is different from the cone `\theta` that is being
integrated over in {eq}`flux_revC` -- that `\theta` is the angle within the cone of `\Delta \omega`.  It is defined as `\theta=0` along the spine of the cone, and it varies to either side by only a few hundredths of a degree if the cone is narrow. The figure below shows the difference between the solar zenith angle `\theta_0` and the cone integration variable `\theta` for mid-afternoon sun.

```{image} ./figures/solar_zenith.png
:width: 40.0%
```

### Isotropic approximation -- L the same at all angles

The other important case is when `L` is the same at all angles -- this is called
isotropic (i.e. direction independent) diffuse radiation.  In that case `L` can come
out {eq}`flux_revB` but `\theta` has to stay in:

$$
E = 2 \pi L \int_0^{\pi/2}  \cos \theta  \sin \theta\, d\theta =
2 \pi \int_0^{1} \mu \, d\mu = 2 \pi L \, \left . \frac{\mu^2}{2} \right |_0^1 = \pi L
$$ (flux_rev_iso)

which is true for a thermal emitter with a infinite horizontal extent (so that we can integrate
all the way down to the horizon).

### L varying over a range of angles

The atmosphere introduces an absorbing emitting layers that changes the value of L
with changing zenith angle.  How do we solve  to solve {eq}`flux_revB` when
`L` an't come out of the integral?  There is a simple approximation
for that situation as well, discussed in {ref}`week11_flux_schwartzchild`

% # radius of sun 695508 km = 700000e.3 = 7.e8
% # earth sun distance  150e6 km = 150.e9
