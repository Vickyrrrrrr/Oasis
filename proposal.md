# PROJECT PROPOSAL: The OASIS Pod
**Off-Grid Autonomous Shelter for Indian Summers**

## 1. Executive Summary
In regions experiencing severe climate shifts, extreme summer temperatures (often exceeding 45°C) combined with heavy monsoon humidity create deadly "Heat Index" conditions. Traditional Air Conditioning is unviable for emergency and low-income shelters because it demands massive amounts of continuous power and relies on a stable electrical grid—which frequently fails during peak heat waves.

**OASIS** is a scalable, off-grid emergency shelter designed to replace power-hungry refrigeration cycles with smart thermal physics and Edge AI. Operating entirely on a highly efficient 12V solar microgrid, the OASIS Pod utilizes a passive Phase Change Material (PCM) thermal battery managed by an autonomous ESP32 microcontroller to maintain safe internal temperatures.

---

## 2. The Core Technology
The OASIS system is built on a 3-part hybrid architecture:

### A. The Passive Thermal Battery (Structural Walls)
Instead of fighting the heat with active compressors, the shelter uses the environment to its advantage. The shelter's walls are integrated with Phase Change Materials (PCM) and coated in a highly reflective, radiative MgO-PVDF skin. During the brutal 45°C day, the PCM silently melts, absorbing massive amounts of thermal energy (latent heat) without raising the ambient temperature. This passively locks the internal room temperature at a safe 28°C threshold.

### B. The Edge AI Brain (Predictive Logic)
The shelter's "nervous system" is powered by a low-cost ($5) ESP32 microcontroller running a custom Machine Learning model (TensorFlow Lite). By continuously monitoring internal and external DHT22 environmental sensors, the Edge AI predicts incoming thermal loads and dangerous humidity spikes, allowing the shelter to react autonomously without human intervention or cloud-dependency.

### C. Active Environmental Control (Actuation)
To "recharge" the thermal battery and manage humidity, the AI manages two specific mechanical subsystems:
*   **The Night-Flush Cycle (Sensible Cooling):** When the AI detects that the outside night air has dropped below the internal temperature, it swings the servo-controlled vents open to 90° and engages heavy-duty 60W intake fans. This blasts cool night air into the wall cavities to "re-freeze" the PCM, fully recharging the thermal battery for the next day.
*   **The Desiccant Loop (Latent Cooling):** During monsoons, a 30°C room can feel like a deadly 35°C due to high humidity. When the AI detects a humidity threshold breach (RH > 55%), it engages a desiccant circulation fan loop to actively scrub moisture from the air, lowering the effective Heat Index.

---

## 3. Simulation & Validation
All thermal dynamics, AI logic, and power economics were rigorously modeled and validated using Python in a Colab simulation environment before moving to physical hardware.

### A. Passive Cooling & Thermal Mass Modeling
We simulated diurnal temperature swings (Lucknow peak 45°C, low 30°C) for a 24-hour cycle using a sine-wave ambient model with 800 W/m² solar flux. The shelter parameters used R-value = 2.5, MgO-PVDF radiative cooling power of 100 W/m² (95% solar rejection), and PCM mass of 50 kg/m² with latent heat of 200 kJ/kg (total capacity of 10,000 kJ/m²). The simulation proves the PCM successfully absorbs the 12-hour thermal load, locking internal temperature at a safe 28°C despite 45°C ambient heat.

![OASIS Shelter 2.0: 24-Hour Passive Cooling Performance](./source/graph_1_passive_cooling.png)

### B. The Humidity Crisis: Why Dry Bulb Temperature is Not Enough
A critical finding emerged when we simulated the shelter under real occupancy conditions. With 8 humans inside (each radiating ~100W of metabolic heat) and ambient humidity ranging from 30% to 70%, the PCM maintained the dry-bulb temperature at 28°C. However, the "Feels Like" Heat Index—calculated using the full psychrometric Magnus-Tetens formula—spiked above the 35°C danger threshold. This proves that sensible cooling alone is insufficient during Indian monsoons; latent heat (humidity) must also be managed.

![OASIS 3.0: Performance with 8 Humans & Humidity - Heat Index Spikes Above Danger Threshold](./source/graph_2b_humans_heatindex.png)

### C. The Desiccant-PCM Stack: Beating the Monsoon Latent Load
To solve the humidity crisis, we simulated an extreme monsoon edge-case (Lucknow, 50-90% RH, 27-37°C ambient). We modeled a 2-stage system: (1) a desiccant scrubber removing 60% of moisture from the incoming air, and (2) the PCM thermal battery cooling the dehumidified air back to 28°C. Despite the thermodynamic paradox where removing water releases latent heat (superheating the air), the PCM successfully re-cools the air, and the final Heat Index stays below the 35°C danger threshold—and even below the 30°C comfort threshold during most hours. Without the desiccant, the standard PCM-only shelter would exceed 35°C HI for 12+ hours a day.

![Extreme Edge Case: Beating the Lucknow Monsoon Latent Load](./source/graph_3_monsoon.png)

### D. Economic Sweet Spot Optimization
To ensure feasibility for low-income applications, we ran a multi-variable parametric sweep mapping PCM Mass (10-50 kg/m²) against Wall Insulation R-Value (0.5-3.0). This heatmap reveals the "economic sweet spot": the minimum material cost that still guarantees a 28°C internal max temperature. The optimizer found that 30 kg/m² PCM with R-2.0 insulation achieves the target—proving the shelter can be built with affordable, globally available materials.

![Optimization: Finding the 'Economic Sweet Spot'](./source/graph_4_optimization.png)

### E. Power System Sizing & Cost Analysis
The entire active system—ESP32 (1.5W), two 30W intake fans (8 hrs/night), desiccant fan (20W, 12 hrs/day), and servo (5W, peak)—consumes only **756.50 Watt-hours per day**. This low energy footprint means the system can be powered by a single **218.54W solar panel** and a **157.60 Ah 12V LiFePO4 battery** (with 2 days of autonomy and 80% depth of discharge).

**Estimated Bill of Materials (Example Baseline Unit):**
| Component | Cost (USD) | Cost (INR) |
|-----------|-----------|-----------|
| Solar Panels (mono, 220W) | ~$79 | ₹6,556 |
| LiFePO4 Battery (12V, 160Ah) | ~$228 | ₹18,912 |
| MPPT Charge Controller | ~$18 | ₹1,500 |
| ESP32, Sensors, Relays, Servo | ~$24 | ₹2,000 |
| PCM Thermal Battery (~30 kg/m², bulk construction-grade paraffin) | ~$1,200–$3,600* | ₹1,00,000–₹3,00,000* |
| PCM Encapsulation & Wall Integration | ~$100–$200 | ₹8,500–₹17,000 |
| **Total (excluding PCM)** | **~$350** | **₹28,969** |
| **Total (with PCM, estimated range)** | **~$1,650–$4,150** | **₹1,37,000–₹3,45,000** |

*PCM prices vary significantly by supplier, purity, quantity, and market conditions. Bulk construction-grade paraffin PCM from Asian markets ranges from $4–$8/kg at scale (1,000kg+ MOQ), while specialty bio-based or encapsulated grades can reach $15–$30/kg. Prices sourced from current Asian and Indian suppliers (2025–2026); actual costs depend on local availability, import duties, and order volume. The lower end reflects direct bulk procurement from Chinese manufacturers; the upper end reflects smaller quantities or locally sourced material in India.*

### F. Machine Learning & Digital Twin Validation
The predictive AI was trained on a **48-hour synthetic weather dataset** (2,880 minute-by-minute rows) using a Random Forest model (50 estimators, max depth 5), achieving a **Mean Squared Error of 1.19** when predicting optimal desiccant fan speeds. A TensorFlow Lite version of the model was exported to a **1,860-byte C++ hex array**, ready for direct flash onto the ESP32. This entire control system was then translated into an interactive 3D WebGL Digital Twin to visually validate spatial feasibility and real-time actuation logic.

---

## 4. Feasibility and Scalability
Because the OASIS Pod does not rely on traditional HVAC compressors, the energy footprint is reduced by over 90%. The entire active system (ESP32, servos, and DC fans) requires only **756.50 Wh/day**—less than a single modern refrigerator. At a total power-system cost of under **₹29,000 ($350)**, the shelter is an order of magnitude cheaper than any equivalent off-grid air conditioning solution.

The OASIS Pod is highly scalable: it can be rapidly deployed in rural areas, disaster relief zones, or urban slums where grid power is unreliable or non-existent. The materials (PCM, structural insulation, basic DC fans) are cheap, globally available, and require virtually zero maintenance compared to refrigerant-based air conditioners.

---

## 5. Conclusion and Impact
The OASIS Pod represents a paradigm shift in climate resilience. By merging ancient passive cooling techniques (thermal mass and night flushing) with modern Edge AI and advanced materials, we have created a life-saving infrastructure that is both economically viable and ecologically sustainable. OASIS does not just temporarily cool a room; it actively protects vulnerable populations from the worsening realities of climate change.
