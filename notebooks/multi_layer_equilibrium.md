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
---

+++ {"user_expressions": []}

(nb:multi-layer)=
# Radiative equilibrium

This script calculates pure radiative equilbrium for an nlayer atmosphere over a black surface, where
each layer has identical transmissivity and the total transmissivity is:

$$
t_{rtot} = (t_{rlayer})^{nlayers}
$$

Constants:

* $z_{top}$ = 15 km -- tropospheric height
* $t_{rtot}$ = 0.2 -- total transmissivity
* $H$ = 8 km -- atmospheric scale height

```{code-cell} ipython3
import numpy as np
import pprint
from numpy.linalg import solve

pp = pprint.PrettyPrinter(indent=4)
np.set_printoptions(precision=2, threshold=50)
```

+++ {"user_expressions": []}

## Five layer atmosphere

Let $A_n$ be the flux from layer n and $G$ be the flux from the black surface.  If upward fluxes are
negative and downward fluxes are positive, the the flux at each of the six levels in this problem
are shown in the sketch below, where $Tr = \exp(-\tau)$ is the longwave transmissivity of each layer.

+++ {"user_expressions": []}

```{figure} ./figures/five_layer_sketch.jpg
---
name: fig:layers
width: 30%
---
Five layers
```

+++ {"user_expressions": []}

That produces a matrix $M$ which looks like this:

+++ {"user_expressions": []}

```{figure} ./figures/equil_fivelayer.jpg
---
name: fig:matrix
width: 30%
---
sparse matrix
```

+++ {"user_expressions": []}

## Creating the matrix equation

The idea is to solve the equilibrium problem as six equations in six unknowns,
where the unknowns are 

$$\vec{E} = \left [A_4, A_3, A_2, A_1, A_0, G \right ]$$

and the at each level, we need to have an upward flux of
$\vec{S_0} = \left [ -S_0, -S_0, -S_0, -S_0, -S_0 , -S_0 \right ]$ to balance
the downwelling shortwave flux at that level.

$$
M \times \vec{E} = \vec{S_0}
$$

+++ {"user_expressions": []}

## five layer example

The off-diagonal elements will be created with the `numpy.diag` function which
requires a vector v of the proper length, and the number of the diagonal, $d$.

```{code-cell} ipython3
nlayers=5
tot_trans = 0.2
H = 8 #km
S = 240 #W/m^2
```

+++ {"user_expressions": []}

### create all the off-diagonal elements of the matrix $M$.

```{code-cell} ipython3
dvals = np.arange(nlayers,-(nlayers+1),-1,dtype=int)
dvals
```

+++ {"user_expressions": []}

* Here are the d are the offset values for the matrix  as used by `numpy.diag`:  [https://numpy.org/doc/stable/reference/generated/numpy.diagonal.html](https://numpy.org/doc/stable/reference/generated/numpy.diagonal.html)
  That is, diagonal d for array a is given by a[i, i+d].  So for this example, d=-5 is the diagonal [$Tr^4$] in row 5,column 0, d=+5 is the diagonal [$-Tr^4$] in row 0, column 5, etc.

+++ {"user_expressions": []}

### Split the transmissivity evenly among nlayers

```{code-cell} ipython3
log_layer_trans = np.log(tot_trans)/nlayers
trans = np.exp(log_layer_trans)
print(f"check{(trans**nlayers)=:.3f}, layer trans: {trans:.3}")
```

+++ {"user_expressions": []}

### Find the length of each diagonal

```{code-cell} ipython3
def find_diaglen(dvals,nlayers):
    '''
    Given a set of diagonal offsets as described in 
    https://numpy.org/doc/stable/reference/generated/numpy.diagonal.html
    return a vector of the lengths of each diagonal
    
    Parameters
    ----------
    
    dvals: int vector
       vector containing the postive and negative diagonal offsets
   
    nlayers: int
       number of atmospheric layers
       
    Returns
    -------
    
    keep_len: int vector
       length of each diagonal
    '''
    nlevels=dvals[0] + 1
    keep_len=[]
    for the_d in dvals:
        if the_d >=0:
            diaglen = nlevels - the_d
        else:
            diaglen = nlevels+the_d
        keep_len.append(diaglen)
    return keep_len
```

```{code-cell} ipython3
def find_element(dvals,diaglen,trans):
    '''
    Create a dictionary containing the element (tr**5., tr**4. etc) that should be written
    for each diagonal
    
    Parameters
    ----------
    dvals: int vector
       diagonal offsets
       
    diaglen: int vector
       diagonal lengths, from find_diaglen
       
    trans: float
       layer transmissivity
       
    Returns
    -------
    
    element_dict: dict
       dictionary keyed by diagonal offset, holding the
       diagonal element, the power it's raised to, and the
       length of the diagonal
    
    '''
    nlevels=dvals[0] + 1
    element_dict={}
    for the_d,the_len in zip(dvals,diaglen):
        if the_d >= 0:
            direction = -1
            the_exp=the_d
        else:
            direction = 1
            the_exp=-(the_d+1)
        the_element=(trans**the_exp)*direction
        element_dict[the_d] = {'element':the_element,
                               'exponent':the_exp,'diaglen':the_len}
    return element_dict
            
```

+++ {"user_expressions": []}

### Create the element_dict

```{code-cell} ipython3
diaglen = find_diaglen(dvals,nlayers)
element_dict=find_element(dvals,diaglen,trans)
pp.pprint(element_dict)
```

+++ {"user_expressions": []}

### Build the matrix

Construct the matrix of {numref}`fig:matrix`

```{code-cell} ipython3
def build_matrix(element_dict):
    '''
    Construct the matrix
    
    Parameters
    ----------
    
    element_dict: dict
      dictionary of diagonals, produced by find_element
      
    Returns
    -------
    
    the_mat: 2-d numpy array of floats
       Matrix of layer transmissivity terms
    
    '''
    num_rows = list(element_dict.keys())[0] + 1
    the_mat = np.zeros([num_rows,num_rows])
    for the_d,the_dict in element_dict.items():
        diaglen=the_dict['diaglen']
        the_element=the_dict['element']
        the_diag=np.full(shape=diaglen,fill_value=the_element)
        diag_mat = np.diag(the_diag,k=the_d)
        the_mat+=diag_mat
    return the_mat
```

+++ {"user_expressions": []}

### Solve for the fluxes

```{code-cell} ipython3
A = build_matrix(element_dict)
S= -240
rhs = np.full(shape=nlayers+1,fill_value=S)
fluxes = solve(A,rhs)
print(f"{A=}")
```

+++ {"user_expressions": []}

### Fluxes in W/m^2

```{code-cell} ipython3
print(f"{fluxes=}")
```

+++ {"user_expressions": []}

### Get the temperatures

```{code-cell} ipython3
def calc_temps(fluxes,trans):
    '''
    Calculate the temperatures for the atmospheric layers and the surface
    
    Parameters
    ----------
    
    fluxes: vector float
       fluxes in Watts/m^2
    
    trans: float
       layer transmissivity
       
    Returns
    -------
    
    temps: vector float
      temperatures (K) for the atmosphere (starting from the top) and the surface
    
    '''
    nlayers = len(fluxes) -1
    sigma = 5.67e-8
    eps = 1 - trans
    eps_vec=[eps]*nlayers
    #
    # add the ground emissivity (black surface)
    #
    eps_vec.extend([1])
    temps=[]
    for a_flux, the_eps in zip(fluxes,eps_vec):
        the_temp = (a_flux/(sigma*the_eps))**0.25
        temps.append(the_temp)
    return np.array(temps)
```

```{code-cell} ipython3
temps=calc_temps(fluxes,trans)
print(f"{temps}")
```

+++ {"user_expressions": []}

## Finding the heights of each level

To find the z values for each level, we need to find the total optical depth of the atmosphere, then use the definition 

$$
d\tau = \rho_{atm}
$$

+++ {"user_expressions": []}

$$
\tau_{ftot} = \int_0^{z_{top}} 1.66 \rho_{atm} r_{mix} k_\lambda dz = \int_0^{z_{top}} 1.66 \rho_{0} r_{mix} k_\lambda  \exp(-z/H)dz
$$

+++ {"user_expressions": []}

$$
\tau_{ftot} = 1.66 \rho_0 r_{mix} k_{\lambda} H \left (1 - \exp \left ( -z_{top}/H \right ) \right )
$$

+++ {"user_expressions": []}

$$
t_{rtot}  = \exp \left ( -\tau_{ftot} \right )
$$

+++ {"user_expressions": []}

$$
\tau_{ftot} = - \log (t_{rtot} )
$$
