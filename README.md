This project is a generalization of the 
undergraduate thesis “Prediction of 
movements within the central Maranguape 
region using vector numerical calculus, with 
support from the C++ programming 
language” (João Lucas Cajazeiras Viana, 
IFCE Maranguape campus) for any origin 
and destination pair within the state of 
Ceará, Brazil. It preserves the 
mathematical core of the original work:
•Vector calculus (Vector2D
) for position, velocity, and force.
•Numerical integration of the equations 
of motion using fourth-order Runge–
Kutta (RK4).
•Resultant-force modeling: 
F = F_propulsion + F_gravity + F_friction.
•Uncertainty propagation using the 
Monte Carlo method.

The main difference from the original 
thesis is that, instead of using fixed 
coordinates in Maranguape, 
the simulator projects any latitude/longitude pair in 
Ceará onto a local Cartesian plane and 
applies the same physics model. A 
database containing 20 municipalities and 
localities (
data/ceara_locations.csv
) is included, and custom coordinates can 
also be provided.

Requirements
•g++ 
with C++17 support (tested with GCC 
13).
•make.
•No external libraries are required; the 
project uses only the C++ standard 
library.

Build and Test 
make test   # builds and runs the automated test suite
make all    # builds the simulator executable (bin/simulador)

The test suite (tests/test_main.cpp
) covers vector algebra, geographic utilities 
(local projection and Haversine distance, 
including an approximate Ceará boundary 
check), the RK4 integrator (compared with 
the analytical solution for exponential 
decay), the force model (velocity 
convergence), and the Monte Carlo module 
(fixed-seed reproducibility and statistical 
sanity checks).

Usage 
Using municipality names
Use the locality database in 
data/ceara_locations.csv:
./bin/simulador --origem Maranguape --destino Fortaleza \
                --modal carro --iteracoes 1000 --saida resultado.csv

Using custom coordinates
./bin/simulador --origem-lat -3.89 --origem-lon -38.68 --alt-origem 90 \
                --destino-lat -3.71 --destino-lon -38.54 --alt-destino 20 \
                --modal moto

Available transport modes are caminhada 
(walking, the default), moto 
(motorcycle), and carro (car).
The program prints the deterministic RK4 
trajectory and the Monte Carlo simulation 
statistics. It also exports the results of 
each iteration to a CSV file using 
--saida arquivo.csv 
for further analysis in Python/Pandas, 
Excel, or other tools.

Command-line options
ity database.
--destino <name> Destination name from the locality database.
--origem-lat <latitude> and --origem-lon <longitude> Custom origin coordinates.
--destino-lat <latitude> and --destino-lon <longitude> Custom destination coordinates.
--alt-origem <meters> Custom origin altitude; defaults to 50 .
--alt-destino <meters> Custom destination altitude; defaults to 50 .
--modal <mode> caminhada , moto , or carro ; defaults to caminhada .
--iteracoes <number> Number of Monte Carlo iterations; defaults to 1000 .
--saida <file> Output CSV path; defaults to resultado_monte_carlo.csv .
--dados <file> Locality database path; defaults to data/ceara_locations.csv .

Project structure
include/
  Vector2D.hpp       -- 2D vectors and state (position + velocity)
  GeoUtils.hpp       -- latitude/longitude projection, Haversine distance, and Ceará bounding box
  Location.hpp       -- Location structure and CSV reader
  ForceModel.hpp     -- mode parameters and F = propulsion + gravity + friction
  Integrator.hpp     -- generic RK4 integrator
  MonteCarlo.hpp     -- single simulation and Monte Carlo execution
src/main.cpp         -- command-line interface
tests/test_main.cpp  -- automated test suite
data/ceara_locations.csv -- 20 Ceará municipalities/localities with approximate coordinates and altitude

Mathematical model
The simulator represents the moving 
object as a particle in a two-dimensional 
local Cartesian plane. Latitude and 
longitude are converted to meters using an 
equirectangular local projection centered 
at the origin. The Haversine formula is also 
used to report the geographic distance 
between the two locations.
The resultant force is defined as:
F = F_propulsion + F_gravity + F_friction

Propulsion uses a proportional controller 
that seeks the target speed of the selected 
transport mode. Gravity is approximated 
from the terrain slope along the straight 
path between the two points. Friction is 
modeled as a viscous force opposing the 
current velocity. The equations of motion 
are integrated with fourth-order Runge–
Kutta.
For Monte Carlo propagation, the simulator 
samples target speed, friction, terrain 
amplitude, and terrain phase from 
probability distributions. The default 
random seed is 
42
, which makes the results reproducible for 
the same input parameters.

Limitations inherited from the original thesis
•Altitude is synthetic. It is generated 
using interpolation and a sinusoidal 
perturbation rather than real terrain 
data such as Shuttle Radar Topography 
Mission (SRTM) data or official contour 
lines.
•The locality coordinates are 
approximate reference points, generally 
representing municipal centers rather 
than exact addresses.
•The model treats the moving object as 
a particle and does not use real road 
routes. The straight line between the 
origin and destination is used as the 
travel axis, as in the original thesis.
•The Ceará validation uses an 
approximate rectangular bounding box. 
It is not a substitute for an official state 
boundary shapefile from the Brazilian 
Institute of Geography and Statistics 
(IBGE).

Suggested next steps
Possible extensions include integration 
with real geographic data from 
OpenStreetMap, routing over an actual 
road network, a graphical interactive 
interface, and the use of official elevation 
data.

References
The mathematical model and terminology 
are based on the undergraduate thesis 
cited in the introduction. No external 
runtime dependencies are required by this 
implementation.
[1] OpenStreetMap
[2] Brazilian Institute of Geography and 
Statistics (IBGE)
