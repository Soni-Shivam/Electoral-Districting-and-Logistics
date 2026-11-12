# Election Logistics in Maharashtra

IE301 Operations Analysis · IEOR, IIT Bombay · Group 4

Pari Soni (24b4505) · Shivam Soni (24b4525) · Shreyaan Sharma (24b4529) · Asmaniwala Devanshu (26x0053) · S Ganesh (26x0052)
Mentor: Prof. Ketan Savla

[Final presentation (PDF)](docs/presentations/04_final_presentation.pdf) · [Technical report](output/pdf/election_logistics_technical_report.pdf) · [All presentations](docs/presentations/)

An operations-research pipeline for Indian Assembly elections, applied to four Maharashtra districts (Sangli, Satara, Nashik, Pune; 52 seats, 1.9 crore electors). It redraws constituencies with ReCom districting, places polling stations with a mixed-integer program, routes EVMs with vehicle routing, and simulates polling-day queues. Results are compared with the 2008 seats and validated against the 2024 election.

![Title slide: Election Logistics, with Nashik (15 seats), Pune (21), Satara (8) and Sangli (8) highlighted on the map of Maharashtra](docs/presentations/final_slides/slide-01.png)
![Problem statement: constituency design (population balance within ±10%, contiguity, compactness) and polling logistics (1,200 electors per station, 2 km walk, station cost, EVM transport)](docs/presentations/final_slides/slide-02.png)
![Approach: five steps (draw seats with ReCom, place stations with a MIP, route EVMs, simulate queues, compare with today), applied to Sangli, Satara, Nashik and Pune](docs/presentations/final_slides/slide-03.png)

## Districting

Seats are drawn on the village border graph with ReCom and simulated annealing. Every seat is connected and within ±10% of the district's average. The score weighs population, distance, taluka splits and compactness. All 52 seats fall inside ±10% in every map; today 26 of 52 are outside.

![ReCom districting model: border graph of villages and wards, weighted score with three weight profiles, merge-tree-cut search with simulated annealing](docs/presentations/final_slides/slide-04.png)
![Districting results for each district under the Balanced, Locality and Taluka weightings, with max deviation, taluka cuts and score](docs/presentations/final_slides/slide-05.png)
![Comparison with the 2008 constituencies: seats outside ±10% fall to zero in every district, at the cost of more taluka cuts](docs/presentations/final_slides/slide-06.png)

## Polling stations

Census 2011 counts are scaled to the 2024 rolls and spread over WorldPop 100 m settlements; walks are measured on OpenStreetMap paths. A Gurobi MIP per seat picks premises, stations per premises and one EVM dispatch centre. It opens 20,196 stations at 14,532 premises, with a 0.54 km average walk and 99.6% of electors within 2 km.

![Polling station data pipeline: count voters, find settlements from WorldPop, measure walks on OpenStreetMap, optimise stations](docs/presentations/final_slides/slide-07.png)
![MIP formulation: decision variables, objective (stations, premises, walking, EVM transport) and constraints (1,200 cap, 2 km walk, one dispatch centre)](docs/presentations/final_slides/slide-08.png)
![Polling station results per district: stations, premises, electors per station, average walk and share over 2 km](docs/presentations/final_slides/slide-09.png)

## Logistics

EVM sets and polling teams travel from each seat's dispatch centre by bus (6 sets) or jeep (1 set), solved as vehicle routing with time windows in OR-Tools. 4,773 vehicles deliver 99.6% of sets by 18:00. Shared routes cut vehicle-km by 77%, and choosing the dispatch centre saves another 14%. The plan costs ₹35–45 per elector.

![How EVMs and polling teams move before and after the poll, and the cost heads a district plans for](docs/presentations/final_slides/slide-10.png)
![Our plan vs today's ECI 2024 polling stations and EVM warehouses in Pune and Nashik](docs/presentations/final_slides/slide-11.png)
![Logistics cost of the plan by district: Sangli ₹9.6 cr, Satara ₹11.9 cr, Nashik ₹20.1 cr, Pune ₹30.9 cr](docs/presentations/final_slides/slide-12.png)
![EVM dispatch routing model: what moves, buses and jeeps, the 13:00 to 18:00 window, routes solved per seat](docs/presentations/final_slides/slide-13.png)
![EVM dispatch routing results: bus and jeep routes for Sangli and Pune](docs/presentations/final_slides/slide-14.png)

## Polling day

A queue simulation runs each station for 30 days. Arrivals follow Maharashtra's 2024 hourly turnout, with 40 s per voter and EVM failures. Cutting the cap from 1,500 to 1,200 electors per station drops the average wait from 7.7–12.8 min to 2.2–4.6 min, for about ₹4 more per elector.

![Polling day simulation model: voters per station, arrival curve, single-compartment queue, EVM failures](docs/presentations/final_slides/slide-15.png)
![Polling day results: queue through the day and average wait per voter at 1,500 vs 1,200 electors per station](docs/presentations/final_slides/slide-16.png)

## Validation

The model is within 3% of Pune's real 2024 polling stations (8,712 vs 8,462). Simulated hourly turnout is within ±1 point of the reported curve. The largest misses are Nashik's rural station count (+12.5%) and Pune's city bus fleet (−30%).

![Validation against the 2024 election: station counts, electors per station, buses and turnout, model vs real](docs/presentations/final_slides/slide-17.png)

## Summary

![Summary: 7,553 villages and wards, 52 seats, 20,196 stations, 4,773 vehicles, 3 min average wait, 3% gap to real 2024 stations](docs/presentations/final_slides/slide-18.png)

---

## Code

| Layer | Code |
|---|---|
| Constituencies (ReCom districting) | `code/sangli_recom.py`, `code/prepare_district_inputs.py`, `code/official_district_map.py` |
| Audit of the official 2008 map on real rolls | `code/audit_official_2008.py`, `code/fetch_ac_electors.py` |
| Walking and driving networks (OSM) | `code/logistics/network.py` |
| Demand: 2024 electors, hamlets, walk inside villages | `code/logistics/demand.py` |
| Joint dispatch centre + polling-station location (Gurobi) | `code/logistics/location.py` |
| Dispatch routing (OR-Tools CVRPTW) and sectors | `code/logistics/routing.py` |
| Polling-day queue and EVM-failure simulation | `code/logistics/simulate.py` |
| SC/ST reservation and Lok Sabha seats | `code/logistics/representation.py`, `code/group_lok_sabha.py` |
| Scenario sweep | `code/logistics/scenarios.py` |

The design and every model's formulation are in [docs/BLUEPRINT.md](docs/BLUEPRINT.md). Raw inputs (OSM extract, WorldPop, SHRUG, DataMeet) are not in the repository; the scripts name their sources. SHRUG-derived data is CC BY-NC-SA 4.0.

```bash
python -m venv .venv && .venv/bin/pip install numpy pandas scipy pulp highspy ortools osmium rasterio shapely networkx geopandas pyproj scikit-learn numba gurobipy
cd code
../.venv/bin/python -m logistics.network --district sangli
../.venv/bin/python -m logistics.demand --district sangli
../.venv/bin/python -m logistics.scenarios --districts sangli --stage all
```
