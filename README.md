# capstone-project
# Dynamic Pricing for Urban Parking Lots

## Project Overview
This project builds a dynamic pricing of urban parking lots using real-time data and economic principles. Given 14 parking locations and multiple time-varying inputs (like occupancy, queue length, vehicle type, traffic, and special events), the objective is to create a pricing model that adjusts the parking price intelligently over time.
The model ingests streaming data(obtained from csv file), evaluate demand and competition, and update prices accordingly using custom logic built only with NumPy, Pandas, and Pathway.

## Project Architecture & Workflow

### Steps Taken
**1.	Data Ingestion**

* Imported CSV(dataset.csv) with Pandas and streamed via Pathway.
* Converted LastUpdatedDate + LastUpdatedTime to unified timestamps.
* Calculated Occupancy Ratio = Occupancy / Capacity.

**2.	Model 1 – Baseline Linear Model**
* Applied formula: **Price(t+1) = Price(t) + α × (Occupancy / Capacity)**
* Initialized price at $10 and α = 0.5 (tunable)
* Used recursive logic (via Python loop) to compute the price based on previous price.

**3.	Model 2 – Demand-Based Pricing**
* Constructed a demand function using multiple features:
**Demand = α × (Occupancy / Capacity) + β × QueueLength − γ × Traffic + δ × IsSpecialDay + ε × VehicleTypeWeight**

**Variables:**
1.	Occupancy / Capacity: Indicates fullness of the lot.
2.	QueueLength: Adds urgency — more cars waiting means higher demand.
3.	TrafficLevel: Higher congestion lowers demand.
4.	IsSpecialDay: Special days like holiday likely raise demand.
5.	VehicleTypeWeight: e.g., cars (1.0), bikes (0.5), trucks (1.5), cycle(0.1).
   
* Normalized demand between 0 and 1.
* Applied smoothed pricing: **Price(t) = Base × (1 + λ × Demand)**
* Ensured price is bounded between 0.5x and 2x.

**4.	Model 3 – Competitive Pricing**
* Calculated proximity between 2 lots using Haversine formula(not implemented in codes, but used an online calculator).
* Chose 2 lots BHMBCCTHL01(own lot) and BHMBCCMKT01(competitor lot)
* Compared prices and applied competition logic
1.	If lot full & nearby cheaper → reduce price or reroute.
2.	If lot empty & nearby expensive → increase price moderately.
   
**5.	Visualization with Bokeh**
* Rendered dynamic price plots.
* Compared own price vs. competitor lots.

## Assumptions
* Demand varies linearly with features.
* Trends over the dataset are consistent.
* Competition logic based on location(proximity).
* Small price changes can affect lot occupancy.
* Base price=10, Tunable Weights α = 0.6, λ = 0.7, β = 0.3, γ = 0.2, δ = 0.5, ε = 0.4

## Price Behaviour
* **Model 1:** Price keeps on rising (being dependent on previous price). 
* **Model2:** Rises with higher occupancy, longer queues, special day. Drops with low occupancy, higher traffic level, 
* **Model3:** Keeps the prices competitive (below competitors’ price making it attractive)

## Tech Stack Used
* Programming: Python (NumPy, Pandas)
* Data Processing: Pathway
* Visualization: Bokeh
* Notebook: Google Colab

## Architecture Diagram
![image](https://github.com/user-attachments/assets/d4fc509f-7df4-4e21-bf73-26e5b83d11af)

## Visualizations
### Model 1 vs Model 2
Model 1 vs Model 2 (for first 4 lots), Remaining lots can be visualized in the colab output dashboard
![Screenshot 2025-07-09 170516](https://github.com/user-attachments/assets/2e7c2072-8d1a-4891-a44f-1f612ceaa883)
![Screenshot 2025-07-09 170547](https://github.com/user-attachments/assets/ce838fe8-54aa-404b-9a5c-2bd1e3fb922b)


### Model 3: Competition Logic (BHMBCCTHL01 vs BHMBCCMKT01)
Keeps the prices competitive (below competitors’ price making it attractive)
Only a chunk of the plot is shown for clarity, the whole plot can be visualized in the colab output.
Red line is our lot’s(BHMBCCTHL01) initial price, orange line is the competitor’s (BHMBCCMKT01) price and green line is the adjusted price based. Yellow region shows the gap. 
![image](https://github.com/user-attachments/assets/398b93a8-6316-4311-9700-f65b6b1372ab)


