This ontology describes the material properties, obtained by the atomistic simulations in certain conditions.

Properties: bulk mechanical and thermal properties, e.g.: isobaric heat capacity, density, elastic moduli.

Conditions: pressure, temperature, deformation.

Exemplary CQ:
Is there any simulation result showing material property X in conditions Y? Where:

X = {density}

Z = {Pressure = 0 atm, Temperature = 300 Kelvin, Deformation=none}  

Answer is: the simulations of quality V conditions Z for which:
1) it exist a known formula connecting V <---> X
  and
2) conditions Z have non-empty intersection with Y

# Scope of application

## Materials:
  - Pure metals (Fe, Al, Cu, etc.)
  - Alloys
    - Intermetallic alloys (ordered structures, e.g. Al<sub>2</sub>Fe<sub>3</sub>, CuPd, with periodically repeated exact formula)
    - ~~Metal-based solid solutions (mixtures with average formula Ta<sub>2.5</sub>V<sub>3.2</sub>Cr<sub>4.1</sub>, not exactly repeated)~~
   
## Properties
  - Thermal properties
  - Mechanical properties

## Methods
  - Atomistic/Molecular simulations involving:
    - Density Functional Theory
    - Molecular Dynamics / Structure Optimization
    - Monte Carlo

## Conditions:
Thermodynamic variables
  - Pressure
  - Temperature
  - Deformation (~ Volume)
  - ~~Concentration~~
