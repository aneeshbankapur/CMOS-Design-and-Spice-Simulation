# CMOS-Design-and-Spice-Simulation
This project demonstrates a sequence of CMOS design and SPICE simulation experiments following the **Sky130 PDK-based flow**.  
Each task focuses on understanding MOSFET device characteristics, CMOS inverter operation, transient response, and design robustness.

---

## 1. MOSFET Behavior & Id–Vds Characteristics

### Objective
To study the **I–V characteristics** of an NMOS transistor and identify the **linear** and **saturation** regions of operation.

![text](https://github.com/aneeshbankapur/CMOS-Design-and-Spice-Simulation/blob/4bd9c4d2126f5d5e87dcfcadd06d1701c96f7344/Images/nmos.webp)
### Discussion
- For **small Vds**, the MOSFET operates in the **linear (ohmic)** region, where Id increases almost linearly with Vds.  
- As **Vds** increases and reaches **Vgs − Vt**, the device enters **saturation**, where Id becomes nearly constant.  
- These plots help visualize channel formation and pinch-off behavior.

### Procedure
- Create an NMOS device in SPICE using Sky130 parameters.  
- Apply a **DC sweep** for **Vds** from 0 V to 1.8 V.  
- Repeat for multiple **Vgs** values from 0 to 1.8V in steps of 0.2V . 
- Plot **Id vs. Vds** curves for each Vgs.

### Simulation in Ngspice
Run the following commands 
```
git clone https://github.com/kunalg123/sky130CircuitDesignWorkshop.git
cd /sky130CircuitDesignWorkshop/design
ngspice  ngspice day1_nfet_idvds_L2_W5.spice
```

Inside ngspice
```
plot -vdd#branch
```

![text](https://github.com/aneeshbankapur/CMOS-Design-and-Spice-Simulation/blob/f6d2b8fa1fcd040fa46825e194d4566a38f5abf8/Images/id_vs_vds.png)


---
## 2. Threshold Voltage Extraction & Velocity Saturation

### Objective
To extract the **threshold voltage (Vt)** of an NMOS device and analyze **velocity saturation** effects by studying both **Id–Vgs** and **Id–Vds** characteristics under different bias conditions.

### Discussion
- **Threshold Voltage (Vt):**
  - Determined as the point where Id begins to rise rapidly with increasing Vgs.
  - Typically, Vt ≈ 0.4–0.6 V for Sky130 NMOS under nominal conditions.

- **Velocity Saturation:**
  - In **long-channel MOSFETs**, the drain current in saturation follows a **quadratic** relation:  
    **Id = (1/2) * μn * Cox * (W/L) * (Vgs − Vt)²**
  - In **short-channel devices**, due to high electric fields, carriers reach **velocity saturation** and current increases **linearly** with (Vgs − Vt):       
    **Id = μsat * Cox * (W/L) * (Vgs − Vt)**
  - This limits the drive current and affects device speed.

- The **Id–Vds** plot will show:
  - A clear **linear region** at low Vds.
  - A **saturation region** where Id flattens.
  - For smaller channel lengths, the slope in the “saturation” region becomes more gradual, indicating velocity saturation.
    
### Procedure in ngsice
1. **Velocity Saturation (Id–Vds Plot):**
   
   Run the following commands 
   ```
   ngspice  day2_nfet_idvds_L015_W039.spice
   ```

   Inside ngspice
   ```
   plot -vdd#branch
   ```
   ![text](https://github.com/aneeshbankapur/CMOS-Design-and-Spice-Simulation/blob/998d54ee86ccb4a0af8da19f0334ac33d223636f/Images/idvds_voltagesat.png)
   
2. **Threshold Extraction (Id–Vgs Plot):**
   
   Run the following commands 
   ```
   ngspice  day2_nfet_idvgs_L015_W039.spice
   ```

   Inside ngspice
   ```
   plot -vdd#branch
   ```

   Move the cursor along the slope of linear region nearer to x axis, then link point on that x axis to observe the Vt
   ```
   x0 = 0.773626, y0 = 0
   ```
   Hence Vt= 0.773626
   📈 *(Attach plot: Id vs. Vds for different Vgs)*
   
---

## 3. CMOS Inverter: Voltage Transfer Characteristic (VTC)

### Objective
To simulate a **CMOS inverter** and obtain its **Voltage Transfer Characteristic (VTC)**.
**📈Cmos picture**

### Discussion
- The **VTC** shows how the output voltage transitions from logic ‘1’ to logic ‘0’.  
- The **switching threshold (Vm)** occurs at **Vin = Vout**, representing the inverter’s trip point.  
- A balanced inverter (equal rise/fall currents) has **Vm ≈ Vdd/2**.

### Procedure
- Connect a **PMOS** and **NMOS** transistor in a complementary configuration.  
- Apply a **DC input sweep** from 0 V to 1.8V at the inverter input node.  
- Measure **Vout** at the output node.  
- Plot **Vout vs. Vin**.

### Simulation in Ngspice
  Run the following commands 
   ```
   ngspice  day3_inv_vtc_Wp084_Wn036.spice
   ```

   Inside ngspice
   ```
   plot out vs in
   ```
 Click the point where both Vin = Vout, that is your switching threshold(Vm)
 ```
 x0 = 0.87699, y0 = 0.876304
```
![text](https://github.com/aneeshbankapur/CMOS-Design-and-Spice-Simulation/blob/68a869f7aa7da67f403b6486710d6103ee9fffb2/Images/cmos_vtc.png)

---

## 4. Transient Behavior: Rise / Fall Delays

### Objective
To analyze the **dynamic performance** of a CMOS inverter using a transient (time-domain) simulation.

### Discussion
- The inverter’s delay depends on transistor sizes, load capacitance, and supply voltage.  
- Ideally, rise and fall delays should be similar for balanced sizing.  
- These parameters directly impact the **speed** of CMOS logic circuits.

### Procedure
- Apply a **pulse waveform** as the input signal.  
- Observe the **output waveform** in time.  
- Measure:
  - **Rise delay (tpLH):** time for output to rise to 50% of Vdd after input falls to 50%.
  - **Fall delay (tpHL):** time for output to fall to 50% of Vdd after input rises to 50%.
- Compute **average propagation delay** = (tpLH + tpHL) / 2.

### Simulation in Ngspice
  Run the following commands 
   ```
   ngspice  day3_inv_tran_Wp084_Wn036.spice
   ```

   Inside ngspice
   ```
   plot out vs time in 
   ```
  **To calculate rise delay**:
  - CLick on the input and output graph and Vdd/2 (i.e 0.9V) when output is rising
  ```
  x0 = 2.15269e-09, y0 = 0.898864

  x0 = 2.48387e-09, y0 = 0.898864
 ```
  - To calculate the rise delay subtract the X values
  - There for **Rise delay= 331ns**

  **To calculate Fall delay**:
  - CLick on the input and output graph and Vdd/2 (i.e 0.9V) when output is falling
  ```
  x0 = 4.05128e-09, y0 = 0.9

  x0 = 4.33504e-09, y0 = 0.9
 ```
  - To calculate the rise delay subtract the X values
  - There for **Fall delay= 334ns**
    
![text](https://github.com/aneeshbankapur/CMOS-Design-and-Spice-Simulation/blob/b4b8954e67d39738c1e6e71c62ebe87215d3bb89/Images/cmos_delay.png)

---

## 5. Noise Margin / Robustness Analysis

### Objective
To determine the **noise margins** of a CMOS inverter and assess its **robustness** against input noise.

### Discussion
- **Noise Margin (NM)** defines how much unwanted noise the circuit can tolerate without misinterpreting logic levels.  
- Higher **NML** and **NMH** values indicate a more reliable inverter.  
- Balanced designs usually yield **symmetric noise margins**.

### Procedure
- From the **VTC** curve, identify:
  - **VOL:** maximum output low voltage  
  - **VOH:** minimum output high voltage  
  - **VIL:** maximum input voltage recognized as logic ‘0’  
  - **VIH:** minimum input voltage recognized as logic ‘1’
- Compute:
  - **NML = VIL − VOL**  
  - **NMH = VOH − VIH**
    
![text](https://github.com/aneeshbankapur/CMOS-Design-and-Spice-Simulation/blob/2a2ce9f9cdb51030d06b106dc9856a717d22eeee/Images/cmos_noise.jpg)

### Simulation in Ngspice
  Run the following commands 
   ```
   ngspice   day4_inv_noisemargin_wp1_wn036.spice
   ```

   Inside ngspice
   ```
   plot out vs time in 
   ```
  Click on the graph where slope is approximately -1
  Now you get two points in the form of
  (VIL, VOH) and (VIH, VOL)
  ```
  x0 = 0.793617, y0 = 1.66042

  x0 = 0.964894, y0 = 0.129167
 ```
Therefore, **NML = 0.6645** and **NMH = 0.6955**

---

## 6. Power-Supply and Device Variation Studies

### Objective
To study how **supply voltage (Vdd)** and **transistor sizing (W/L)** variations affect inverter performance.

### Discussion
- **Lower Vdd** reduces noise margin and increases delay due to reduced drive strength.  
- **Larger W/L** increases current drive, reducing delay but increasing power consumption.  
- **Unbalanced sizing** shifts the switching threshold away from Vdd/2.

### Procedure for Supply Variation
- Vary **Vdd** 0 to 1.8V and re-run the **VTC** simulation.  
- Observe how **Vm** and overall VTC shape shift with Vdd.
- Observe the gain in each case
  
### Ngspice Simulation of Supply Variation
   Run the following commands 
   ```
   ngspice   day5_inv_supplyvariation_Wp1_Wn036.spice
   ```
   ![text](https://github.com/aneeshbankapur/CMOS-Design-and-Spice-Simulation/blob/461486ca9728be6114d2a997d963e13904c9e71a/Images/cmos_supplyvar.png)
   
   **Observations:**
   - Noise Margins are reduced when power supply decreases
   - Gain increases when power supply decreases

### Procedure for Device Variation
- Change **W/L ratios** of NMOS and PMOS to simulate device mismatch conditions( i.e Wp=2.5*Wn).  
- Re-evaluate **Vm** and **noise margins**

### Ngspice Simulation of Supply Variation
   Run the following commands 
   ```
   ngspice  day5_inv_devicevariation_wp7_wn042.spice
   ```

   Inside ngspice
   ```
   plot out vs time in 
   ```
   ![text](https://github.com/aneeshbankapur/CMOS-Design-and-Spice-Simulation/blob/315277ccc7d7ccf23a973fcfba9e071b6933c363/Images/cmos_devicevar.png)

  **Observations:**
   - As Pfet is stronger than Nfet we observe that the device is hold the value of vdd more that 0 and output
   - Vm shifts towards right and becomes equla to 0.98V

---
## 🧠 Key Learnings

- Understood **MOSFET operating regions** through I–V analysis.  
- Extracted **threshold voltage (Vt)** and observed **velocity saturation**.  
- Simulated a **CMOS inverter** and obtained its **VTC** and **switching threshold**.  
- Analyzed **dynamic delays** and **noise margins**.  
- Investigated how **Vdd** and **device sizing** affect inverter performance.  

---
## 🏁 Conclusion

This CMOS design and simulation project provided comprehensive insights into MOSFET device physics and inverter behavior.  
By performing DC, transient, and parametric analyses, key aspects of **CMOS switching, delay, and robustness** were explored.  
These foundational experiments are essential for understanding **digital VLSI circuit design** and **timing optimization** in modern semiconductor technology.

---

   
