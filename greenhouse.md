---
jupytext:
  formats: ipynb,md:myst
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.14.5
kernelspec:
  display_name: Python 3 (ipykernel)
  language: python
  name: python3
toc-autonumbering: true
toc-showcode: true
---

+++ {"user_expressions": []}

# The greenhouse effect

## Definition

The greenhouse effect is the change in longwave radiation escaping to space due to the presence of an atmosphere.

(Note that this doesn't have much to do with actual greenhouses)

### A concrete example -- the Earth's current atmosphere

GH = -239 $W\,m^{-2}$ - (-398) $W\,m^{-2}$  = 159 $W\,m^{-2}$


```{figure} figures/cartoon_budget.png
---
name: cartoon_budget
width: 35%
---

```

+++ {"user_expressions": []}

## Outline

1. Define some terms

1. Build a model of atmospheric absorption and emission

1. Use the model to calculate the greenhouse effect and the atmospheric heating rate

+++ {"user_expressions": []}

## Definitions: [flux](https://en.wikipedia.org/wiki/Radiometry)

* Flux density (or irradiance)

  Units: $W\,m^{-2}$
  
  Definition: electromagnetic energy passing through unit area in unit time
  
  Symbol: E
  
  (for physicists -- this is $S$, the [Poynting vector](https://en.wikipedia.org/wiki/Poynting_vector) )
  
  Not conserved in a vacuum -- flux decreases as $1/\text{distance}^2$
  
* Some assumptions for undergraduates

  - Shortwave flux: all photons with wavelengths $\lt$ 3 $\mu m$ are emitted by the sun
  - Longwave flux: all photons with wavelengths $\gt$ 3 $\mu m$ are emitted by terestrial solids, fluids or gasses
  - Shortwave flux from the sun (the *direct beam*) is directional/collimated, scattering dominates
  - Longwave flux is isotropic, scattering is negligible

```{figure} figures/cartoon_budget.png
---
name: cartoon_budget
width: 25%
---

```

+++ {"user_expressions": []}

## Definitions: [radiance](https://en.wikipedia.org/wiki/Radiometry)

* Radiance

  Units: $W\,m^{-2}\,sr^{-1}$
  
  Definition: electromagnetic energy passing though unit area in unit time in unit field of view
  
  Symbol: L
  
  Conserved in a vacuum
  
  Dilemma: climate scientist want to know the flux, but can only measure the radiance

+++ {"user_expressions": []}

## Integrating the radiance to get flux

```{figure} figures/solid_angle.png
---
name: test label
width: 25%
---
```


+++ {"user_expressions": []}

## Radiance and flux


## Measuring the greenhouse


## Longwave vs. shortwave


## Band saturation


##  Absorption bands


## absorption, transmission reflection


## Beer's law


## The schwartzchild equation



## The heating rate

+++ {"user_expressions": []}

# The greenhouse effec
