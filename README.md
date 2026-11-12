# Election Logistics in Maharashtra

IE301 Operations Analysis · IEOR, IIT Bombay · Group 4

Pari Soni (24b4505) · Shivam Soni (24b4525) · Shreyaan Sharma (24b4529) · Asmaniwala Devanshu (26x0053) · S Ganesh (26x0052)
Mentor: Prof. Ketan Savla

[Final presentation (PDF)](docs/presentations/04_final_presentation.pdf) · [Technical report](output/pdf/election_logistics_technical_report.pdf) · [All presentations](docs/presentations/)

![](docs/presentations/final_slides/slide-01.png)
![](docs/presentations/final_slides/slide-02.png)
![](docs/presentations/final_slides/slide-03.png)

## Districting

![](docs/presentations/final_slides/slide-04.png)
![](docs/presentations/final_slides/slide-05.png)
![](docs/presentations/final_slides/slide-06.png)

## Polling stations

![](docs/presentations/final_slides/slide-07.png)
![](docs/presentations/final_slides/slide-08.png)
![](docs/presentations/final_slides/slide-09.png)

## Logistics

![](docs/presentations/final_slides/slide-10.png)
![](docs/presentations/final_slides/slide-11.png)
![](docs/presentations/final_slides/slide-12.png)
![](docs/presentations/final_slides/slide-13.png)
![](docs/presentations/final_slides/slide-14.png)

## Polling day

![](docs/presentations/final_slides/slide-15.png)
![](docs/presentations/final_slides/slide-16.png)

## Validation

![](docs/presentations/final_slides/slide-17.png)

## Summary

![](docs/presentations/final_slides/slide-18.png)

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
