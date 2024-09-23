This ontology describes the material properties, obtained by the atomistic simulations in certain conditions.

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
# Is there any simulation result showing Bulk Modulus (B) of Nb at Temperature=300K and Pressure=0 atm? 

property = {Bulk Modulus}
material = {Niobium (Nb)}
conditions = {Pressure = 1 atm, Temperature = 300 Kelvin, Deformation=none}  

Answer to this CQ see below in SPARQL section.

# Ontology schema

The ontology is designed in blocks. First block is a PMD core based physical experiment. Dashed properties are not used and are added for illustration purposes. The attached legend is valid for all provided figures.

![exper](https://github.com/user-attachments/assets/4680c33a-718b-4503-afc2-b845e76a1ebb)

Next block is simulation process, which involves input and output as information content entities simulating the physical material and its properties (both qualities and dispositions from BFO-2020 perspective). Each simulation process takes one set of input parameters and provides one output value (of the corresponding property).

![Screenshot 2024-09-23 103607](https://github.com/user-attachments/assets/64276803-131f-4d4e-bccd-5dfce54173e7)

The last block contains The simulation plan which contains all processes along with their values of input paramages(Qualities in BFO sense) and outputs (Dispositions in BFO sense. As well provides "explaination" of the calclated property, by relating it to free energy. Desired input and output of the simulation plan provide basis for prospective data linking of the simulation results, as you can see in SPARQL query section.

![Screenshot 2024-09-23 095239](https://github.com/user-attachments/assets/8c863c48-ddc4-495d-a526-ab5eba1ef4c3)

# Patterns used in ontology

Scalar value specification tells us that certain quantity has measure and unit:

![Screenshot 2024-09-23 095559](https://github.com/user-attachments/assets/66c2472a-0562-44d7-b76f-301b25af4bc6)

Definitions of simulated entities (subclass of IAO:data item)

![Screenshot 2024-09-23 095456](https://github.com/user-attachments/assets/c64ba0b3-a8cb-417d-b6fa-cb91cdb63201)

Parametric dimension explains ranges of parameters involved in simulations (corresponding to thermodynamic conditions), And allows for calculating the intersections :

![Screenshot 2024-09-23 095545](https://github.com/user-attachments/assets/7cf1801a-dd61-4f89-be92-f28928b9de8c)

Relation to free energy allows for connecting different properties with each other. It does not provide the exact formula however it shows the possibility of deriving one simulation result from another.

See example for bulk modulus:
![Screenshot 2024-09-23 095329](https://github.com/user-attachments/assets/e21f2edc-8e4e-4446-a1da-c26568539e33)
And for heat expansion:
![Screenshot 2024-09-23 095321](https://github.com/user-attachments/assets/0c38db30-c533-43ab-849f-c1e9e19d0ba2)

# SPARQL queries

These queries can be executed over the provided ontology, after downloading the files and launching the HermiT reasoner. They show how a single property (bulk modulus) can be obtained from two different simulations. This is an exaple of semantic linking of different data sources:

![Uploading TOTAL0.png…]()

1. First query asks for a simulation plan directly providing bulk modulus for a temperature between 0K and 500K:
   
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX pmd:<https://w3id.org/pmd/co/>
PREFIX obo:<http://purl.obolibrary.org/obo/>

SELECT ?plan ?resvalue ?lvalue ?uvalue ?unit
WHERE {
?plan a pmd:SimulationPlan.
?plan pmd:hasOutputDisposition ?resvalue.
?resvalue a pmd:SimulatedBulkModulus.
?plan pmd:hasInputParameterSpace ?ispace.
?ispace pmd:hasDimension ?interval.
?interval a pmd:TemperatureDimension.
?interval pmd:hasLowerBoundary ?lbound.
?interval pmd:hasUpperBoundary ?ubound.
?lbound <http://purl.obolibrary.org/obo/OBI_0001937> ?lvalue .
?ubound <http://purl.obolibrary.org/obo/OBI_0001937> ?uvalue .
?lbound obo:IAO_0000039 ?unit
FILTER (?lvalue>=0 && ?uvalue<=500)
}

2. Second query asks for a simulation plan providing free energy values for >=3 different volume values and for 0K-500K temperature range, which allows for calculating bulk modulus via 2nd derivative relation.

PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX pmd:<https://w3id.org/pmd/co/>
PREFIX obo:<http://purl.obolibrary.org/obo/>

SELECT ?plan ?resvalue ?lvalue ?uvalue ?unit
WHERE {
?plan a pmd:SimulationPlan.
?plan pmd:hasOutputDisposition ?resvalue.
?resvalue a pmd:ZeroDerivativeOfEnergy.
?plan pmd:hasInputParameterSpace ?ispace.
?ispace pmd:hasDimension ?intervalT.
?intervalT a pmd:TemperatureDimension.
?intervalT pmd:hasLowerBoundary ?lbound.
?intervalT pmd:hasUpperBoundary ?ubound.
?lbound <http://purl.obolibrary.org/obo/OBI_0001937> ?lvalue .
?ubound <http://purl.obolibrary.org/obo/OBI_0001937> ?uvalue .
?lbound obo:IAO_0000039 ?unit.
?ispace pmd:hasDimension ?intervalV.
?intervalV a pmd:VolumeDimension.
?intervalV pmd:hasNumberOfPoints ?vpoints
FILTER (?lvalue>=0 && ?uvalue<=500 && ?vpoints>=3)
}

# Contact
[Dr. Konstantin Gubaev](https://www.fiz-karlsruhe.de/en/forschung/lebenslauf-und-publikationen-dr-kostiantyn-hubaiev)
