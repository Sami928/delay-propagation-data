======================================================================
  Benchmark Instances for Distributionally Robust Aircraft Routing
======================================================================

These benchmark instances accompany the paper:

    "An Exact Copositive Reformulation for Distributionally Robust
     Aircraft Routing"
    Jingying Li, Wenxuan Shan, Chung-Piaw Teo


1. PROBLEM DESCRIPTION
----------------------------------------------------------------------

Given a fleet of k aircraft and a set of n scheduled flights within a
hub-and-spoke network, the goal is to assign flights to aircraft
(routing) so as to minimize the worst-case expected total propagated
delay. The worst case is taken over all delay distributions matching
the observed first and second moments.

Delay propagates along each aircraft's route via the Lindley recursion:

    w_j = max{0, w_i + gamma_i - B_ij}

where w_j is the propagated delay at flight j, gamma_i is the primary
delay of flight i, and B_ij is the ground buffer time on arc (i, j).


2. INSTANCE SUMMARY
----------------------------------------------------------------------

  Instance  Aircraft (k)  Days  Flights (n)  Arcs (m)
  --------  ------------  ----  -----------  --------
  A2_D2     2             2     12           13
  A2_D3     2             3     18           23
  A2_D5     2             5     30           88
  A3_D2     3             2     13           21
  A3_D3     3             3     19           24
  A3_D5     3             5     31           126
  A5_D2     5             2     24           37
  A5_D3     5             3     36           62
  A5_D5     5             5     59           108


3. NETWORK CONSTRUCTION
----------------------------------------------------------------------

Each instance is constructed from real airline operational data:

(1) Flight selection. Given fleet size k and planning horizon D days,
    we select the flights operated by k aircraft whose scheduled
    departure times fall within a D-day window.

(2) Arc generation. A directed arc (i, j) is created between flights
    i and j if:
    - the destination airport of flight i equals the origin airport
      of flight j,
    - the scheduled arrival of i precedes the scheduled departure
      of j, and
    - the ground time g_ij = STD_j - STA_i satisfies
      T_min <= g_ij <= T_max.

(3) Buffer computation. The buffer on each arc is B_ij = g_ij - T_min.

(4) Delay distributions. The mean and variance of primary arrival
    delay for each flight are estimated from historical records.


4. FILE FORMAT
----------------------------------------------------------------------

Each instance folder contains:

  Nodes_set.csv
      Flight information. Columns:
      - Index: flight index (0-based)
      - Operating Flight Number-Sector: anonymized flight sector
        (format: F{index}-{ORI}-{DES})
      - Scheduled Departure Datetime Utc (STD): departure time
      - Scheduled Arrival Datetime Utc (STA): arrival time
      - ORI: origin airport index
      - DES: destination airport index
      - Expected arrival delay: mean of primary arrival delay (min)
      - Variance of arrival delay: variance of primary arrival delay

  AdjacencyMatrix.csv
      (n+2) x (n+2) binary adjacency matrix, no header.
      - Row/column 0: source node (aircraft initial state)
      - Rows/columns 1 to n: flights
      - Row/column n+1: sink node
      Entry (i, j) = 1 if arc (i, j) exists.

  BufferMatrix.csv
      (n+2) x (n+2) buffer matrix (minutes), no header.
      Same indexing as the adjacency matrix.

  PDF_step.csv
      Segment-based probability density function of primary delay
      for each flight. Columns:
      - Node_index: flight index
      - Segment_index: segment index
      - Segment_PDF: probability density value
      Segment width = 5 minutes.

  Actual_route.csv
      Actual airline rotation schedule (anonymized). Columns:
      - Aircraft_index: aircraft identifier (anonymized, 0-based)
      - Length: number of flights in rotation
      - Rotaion: flight sequence (1-based, arrow-separated)

  result/SDP/
      Optimal solutions from the SDP-based solver (copositive
      formulation with branch-and-price). Files:
      - Result_BCP_UB_BEST_*.csv: upper bound (best integer solution)
      - Result_BCP_LB_BEST_*.csv: lower bound (LP relaxation)

  result/expect/
      Optimal solutions from the expectation-based solver (FFT
      convolution with branch-and-price). Same file format.

  flight_network_*.png
      Visualization of the flight network:
      (a) Flight selection with actual airline routes
      (b) Network construction showing all feasible arcs


5. NODE INDEXING CONVENTION
----------------------------------------------------------------------

  Node 0:        Source (aircraft initial state)
  Nodes 1 to n:  Flights (0-based flight index f maps to node f+1)
  Node n+1:      Sink

The adjacency and buffer matrices use this (n+2)-node indexing.
The route files (Actual_route.csv, result files) use 1-based flight
indexing in route sequences and 0-based in assigned flight arrays.


6. AIRPORT INDEXING
----------------------------------------------------------------------

Airports are indexed numerically. The hub airport is index 0.
The flight sector field uses 3-letter airport codes (e.g., SIN, BKK,
ICN) derived from IATA codes.


7. CITATION
----------------------------------------------------------------------

If you use these instances, please cite:

    @unpublished{li2026copositive,                                                                                     
    author  = {Li, Jingying and Shan, Wenxuan and Teo, Chung-Piaw},
    title   = {An Exact Copositive Reformulation for Distributionally Robust Aircraft Routing},                      
    note    = {Submitted to Journal of Optimization Theory and Applications},                                        
    year    = {2026}                                                                                                 
    }   


8. LICENSE
----------------------------------------------------------------------

These benchmark instances are derived from real airline operational
data and have been anonymized. They are provided for academic research
purposes only.
