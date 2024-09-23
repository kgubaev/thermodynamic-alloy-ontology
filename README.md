This ontology describes the material properties in certain (thermodynamic) conditions, obtained by the atomistic simulations .

# Table of Contents  
[Scope of application](#Scope-of-application)  

[Exemplary Competency Question](#Exemplary-CQ)

[Ontology Schema](#Ontology-Schema)

[SPARQL queries for Competency Question answering](#SPARQL-queries-for-Competency-Question-answering)

[Connection between different data sources][#Exemplary-data-sources-connection]

[Contact](#Contact)

# Scope of application
## Properties
Static, equilibrium, bulk:
  - Thermal properties, e.g., formation energy, heat capacity
  - Mechanical properties, e.g., elastic moduli, thermal expansion
    
   excluding:

  - Charge-related properties, e.g., polarization
  - Electron properties, e.g., HOMO/LUMO
  - Magnetic, Optic properties

## Methods
  - Atomistic/Molecular simulations involving:
  - Density Functional Theory / Interatomic Potentials
  - Molecular Dynamics / Structure Optimization / Monte Carlo

## Conditions:
  - Pressure
  - Temperature
  - Volume (deformation tensor in future)
  - Concentration (for future)
    
## Materials:
Periodic materials
  - Pure metals (Fe, Al, Cu, etc.)
  - Alloys
    o Intermetallic alloys (ordered structures, e.g. Al<sub>2</sub>Fe<sub>3</sub>, CuPd, with periodically repeated exact formula)
    o Metal-based solid solutions (mixtures with average formula Ta<sub>2.5</sub>V<sub>3.2</sub>Cr<sub>4.1</sub>, not exactly repeated) (for future)
      
# Exemplary CQ:
## Is there any simulation result showing Bulk Modulus (B) of Niobium (Nb) at Temperature=300K and Pressure=0 atm? 

property = {Bulk Modulus} </br>
material = {Niobium (Nb)}</br>
conditions = {Pressure = 1 atm, Temperature = 300 Kelvin, Deformation=none}</br>  

Answer to this CQ see below in [SPARQL section](#SPARQL-queries-for-Competency-Question-answering).

# Ontology schema

The ontology is designed in blocks. 

## Experimental process

First block is PMDcore-based physical experiment. Dashed properties are not used and are added for illustration purposes. The attached legend is valid for all provided figures.

![exp_full](https://github.com/user-attachments/assets/4b09416b-1025-4f30-8d3a-abad019383ff)

## Simulation process

Next block is simulation process, which involves input and output as information content entities simulating the physical material and its properties (both qualities and dispositions in BFO-2020 perspective). Each simulation process takes one set of input parameters and provides one output value (of the corresponding property).

![Screenshot 2024-09-23 103607](https://github.com/user-attachments/assets/64276803-131f-4d4e-bccd-5dfce54173e7)

## Simulation Plan

The last block adds the simulation plan which contains all processes along with their values of input paramages (qualities in BFO sense) and outputs (dispositions in BFO sense). As well provides "explanation" of the calculated property, by relating it to free energy. Desired input and output of the simulation plan provide basis for prospective data linking of the simulation results, as you can see in SPARQL query section.

![Screenshot 2024-09-23 095239](https://github.com/user-attachments/assets/8c863c48-ddc4-495d-a526-ab5eba1ef4c3)

# Patterns used in ontology

Scalar value specification tells us that certain quantity has measured value and unit:

![Screenshot 2024-09-23 095559](https://github.com/user-attachments/assets/66c2472a-0562-44d7-b76f-301b25af4bc6)

Definitions of simulated entities (subclass of iao:datum)

![Screenshot 2024-09-23 095456](https://github.com/user-attachments/assets/c64ba0b3-a8cb-417d-b6fa-cb91cdb63201)

Parametric dimension contains ranges of parameters involved in simulations (corresponding to thermodynamic conditions) and the amount of points in each dimension. This allows for calculating the intersections among ranges of parameters and the ability to perform derivation.

![Screenshot 2024-09-23 095545](https://github.com/user-attachments/assets/7cf1801a-dd61-4f89-be92-f28928b9de8c)

Relation to free energy allows for connecting different properties with each other. It does not provide the exact formula, however it shows the possibility of deriving one simulation result from another.

See example for bulk modulus:
![Screenshot 2024-09-23 095329](https://github.com/user-attachments/assets/e21f2edc-8e4e-4446-a1da-c26568539e33)
And for heat expansion:
![Screenshot 2024-09-23 095321](https://github.com/user-attachments/assets/0c38db30-c533-43ab-849f-c1e9e19d0ba2)

# SPARQL queries for Competency Question answering

These queries can be executed over the provided ontology, after downloading the files and launching the HermiT reasoner. They show how a single property (bulk modulus) can be obtained from two different simulations. This is an exaple of semantic linking of different data sources. Proper units conversion possiblity to be added later, by now default SI temperature unts are assumed.  

1. First query asks for a simulation plan directly providing bulk modulus for a temperature between 0K and 500K:
   
PREFIX owl: <http://www.w3.org/2002/07/owl#></br>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#></br>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#></br>
PREFIX pmd:<https://w3id.org/pmd/co/></br>
PREFIX obo:<http://purl.obolibrary.org/obo/></br>
</br>
SELECT ?plan ?resvalue ?lvalue ?uvalue ?unit</br>
WHERE {</br>
?plan a pmd:SimulationPlan.</br>
?plan pmd:hasOutputDisposition ?resvalue.</br>
?resvalue a pmd:SimulatedBulkModulus.</br>
?plan pmd:hasInputParameterSpace ?ispace.</br>
?ispace pmd:hasDimension ?interval.</br>
?interval a pmd:TemperatureDimension.</br>
?interval pmd:hasLowerBoundary ?lbound.</br>
?interval pmd:hasUpperBoundary ?ubound.</br>
?lbound <http://purl.obolibrary.org/obo/OBI_0001937> ?lvalue .</br>
?ubound <http://purl.obolibrary.org/obo/OBI_0001937> ?uvalue .</br>
?lbound obo:IAO_0000039 ?unit</br>
FILTER (?lvalue>=0 && ?uvalue<=500)</br>
}</br>

2. Second query asks for a simulation plan providing free energy values for >=3 different volume values for 0K-500K temperature range, which allows for calculating bulk modulus via 2nd derivative relation.

PREFIX owl: <http://www.w3.org/2002/07/owl#></br>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#></br>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#></br>
PREFIX pmd:<https://w3id.org/pmd/co/></br>
PREFIX obo:<http://purl.obolibrary.org/obo/></br>

SELECT ?plan ?resvalue ?lvalue ?uvalue ?unit</br>
WHERE { </br>
?plan a pmd:SimulationPlan.</br>
?plan pmd:hasOutputDisposition ?resvalue.</br>
?resvalue a pmd:ZeroDerivativeOfEnergy.</br>
?plan pmd:hasInputParameterSpace ?ispace.</br>
?ispace pmd:hasDimension ?intervalT.</br>
?intervalT a pmd:TemperatureDimension.</br>
?intervalT pmd:hasLowerBoundary ?lbound.</br>
?intervalT pmd:hasUpperBoundary ?ubound.</br>
?lbound <http://purl.obolibrary.org/obo/OBI_0001937> ?lvalue .</br>
?ubound <http://purl.obolibrary.org/obo/OBI_0001937> ?uvalue .</br>
?lbound obo:IAO_0000039 ?unit.</br>
?ispace pmd:hasDimension ?intervalV.</br>
?intervalV a pmd:VolumeDimension.</br>
?intervalV pmd:hasNumberOfPoints ?vpoints</br>
FILTER (?lvalue>=0 && ?uvalue<=500 && ?vpoints>=3)</br>
}</br>

# Exemplary data sources connection

Hereby three different data sources (from left to right) are connected via derivative relations. This ontology provides semantic basis for such connections. </br>
 - material science article</br>
 - pyiron result</br>
 - MaterialsProject entity</br>
 
![TOTAL0](https://github.com/user-attachments/assets/5254cfb5-1ee6-4522-bcf1-ac9c3168af98)

# Contact

Dr. Konstantin Gubaev (Kostiantyn Hubaiev)

ORCID ID: https://orcid.org/0000-0003-2612-8515</br>
work e-mail: kostiantyn.hubaiev@fiz-karlsruhe.de</br>
personal e-mail: yamir4eg@gmail.com</br>
