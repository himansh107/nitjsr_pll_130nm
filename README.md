# 130nm Clock mulitplier PLL
Design of on chip clock multiplier PLL using sky130nm library file

## Introduction
A clock multiplier Phase-Locked Loop (PLL) is a negative feedback control system that generates a high-frequency output clock whose phase is related to the low frequency input clock. The need for PLLs is widespread as they find application in analog, digital, RF and communication systems. <br>The low-frequency clock can be generated using a crystal with frequencies up to 200 MHz. Generating a high-frequency clock with high spectral purity is not possible with a crystal and this is where PLLs come into play. <br>

A simple PLL consists of a Phase and Frequency Detector (PFD), Charge Pump (CP), Loop Filter (LF), Voltage Controlled Oscillator (VCO) and a frequency divider in the feeback path <br>

## Specifications

| Parameter | Description | min | val | max | Unit | Conditions |
| --- | --- | --- | --- | --- | --- | --- |
| VDD | Digital Supply | - | 1.8 | - | V | T = 27C |
| F<sub>CLKREF</sub> | Reference | 5 | 9 | 12.5 | MHz | T = 27C |
| F<sub>CLKOUT</sub> | Output Clock | 39.93 | 71.63 | 100.5 | MHz | PLL Mode, T = 27C |
| J<sub>RMS</sub> | Jitter (rms) | - | - | - | ps | PLL_Mode |
| DC | Duty Cycle | 66.65 | 66.11 | 65.92 | % | T = 27C | 
| T<sub>SET</sub> | Settling Time | ~2.5 | - | ~6 | us | T = 27C |
| C<sub>L</sub> | Load Capacitance | - | - | - | fF | T = 27C |
| IDD | Supply Current | - | - | - | fF | T = 27C |


## EDA tools used

1. xschem
2. ngspice

## Intended block diagram 
![Blank diagram](https://github.com/himansh107/VSDCMOS_labs/assets/75253218/b4aa826b-ffd5-4b25-a903-095417aef5d9) </p>

## Pre layout simulations

 ### 1. Phase frequency detector (PFD)  </p>
 A phase frequency detector outputs an UP or a DOWN signal depending upon the timed phase difference between the input signal and reference signal. If the input signal leads the reference signal an UP signal is produced whereas if the input lags the reference signal a down signal is produced. </p>
 
  **Circuit of PFD used** </p>
  ![Screenshot 2024-03-19 221328](https://github.com/himansh107/VSDCMOS_labs/assets/75253218/5f4dd1ca-ac32-4786-a97e-554b49c25abf)
  </p>

  - input signal - f_clk_in
  - reference signal - f_vco

  </p>
There is a 1ns difference between the time period of f_vco and f_clk_in. This is done to emulate phase & frequency error.   </p>

  **Expected output** </p>
  ![PDF](https://github.com/himansh107/nitjsr_pll_130nm/assets/75253218/72426122-950e-4538-9b88-d68a2a7a94b3)

  **Simulated output** </p>

 - considering constant phase difference between input signal and reference signal </p>
  
  ![pfd const phase difference](https://github.com/himansh107/VSDCMOS_labs/assets/75253218/62593d02-524f-4fe3-beb0-b5d544289e8e)
   </p>

   - considering input and reference signal to have different time periods </p>

   ![pfd](https://github.com/himansh107/VSDCMOS_labs/assets/75253218/e8e84f63-12d9-4634-96e7-b8d72d29cae2)
</p>

In the above simulation result when f_clk_in leads f_vco , UP signal is triggered (yellow). While when the f_clk_in lags f_vco , DOWN signal is triggered. 
These two (up & down) signals are fed into the charge pump which is the next block of our implemented PLL.</p>


 ### 2. Charge Pump and Loop filter  </p>

### Charge Pump </p>
 These UP and DOWN pulses are fed into the input of the charge pump. The charge pump is a combination of switches connected to the power supply. This system acts as a current source/sink which injects/draws current into/from the loop filter, based on the duty cycle of UP/DOWN signal. </p>

  **Circuit of Charge Pump used** </p>
![Screenshot 2024-03-20 004414](https://github.com/himansh107/VSDCMOS_labs/assets/75253218/a6503bb3-ee87-4690-8e91-6ce789e45f74)
</p>
In the above schematic transistors M4 and M3 act as current sources while transistors M18 and M10 are current mirrors. The schematic shown above contains the loop filter. One can see it at the Vctrl output pin. </p>

### Loop filter </p>
This charge pump current is fed into the low pass filter. The low pass filter is a capacitor in series with a resistor. The entire system is connected in shunt with the control loop. The duration of pulses from the charge pump decides the amount of charge injected in the capacitor. </p> 

**Circuit of loop filter used** </p>
![loop filter](https://github.com/himansh107/nitjsr_pll_130nm/assets/75253218/07c268e4-ff60-4b9c-b4bd-4323ff4e46ee)

</p>
This charge on the plates of the capacitor generates a voltage, which is then used as the control signal (Vctrl) to the VCO. </p>

**Design considerations of Loop filter**</p>
The loop filter consists of a resistor in series with a capacitor. This combination is in parallel with another capacitor as shown in figure above Capacitor C1 adds an integrator path and introduces a pole. The resistor adds a proportional path and introduces a zero. Thus, the system is stable with the addition of these two paths. Capacitor C2 is used to suppress the Vctrl ripple. However, the addition of an additional capacitor comprises on the stability of the PLL. This is because C2 adds another pole. So, to mitigate this issue the value of C2 is chosen to be smaller than C1 (~C2 = 0.2*C1) </p>

**Expected output (CP+LF)** </p>
![image](https://github.com/himansh107/VSDCMOS_labs/assets/75253218/17e7cefc-83dc-4d28-8b2f-a3c657a7504e)

**Simulated output (CP + LF)** </p>

**Charge pump output for UP signal** </p>
![cp_up](https://github.com/himansh107/nitjsr_pll_130nm/assets/75253218/f8af9b6b-3527-493c-9036-8f0bd2488d93)
</p>

**Charge pump output for DOWN signal** </p>

![cp_down](https://github.com/himansh107/nitjsr_pll_130nm/assets/75253218/8867d62f-db7e-44b9-8661-67b459d0cd79)

### Voltage controlled oscillator  
</p>

The control voltage is fed to a differential ring oscillator VCO. The VCO produces an output which is oscillating in nature. The frequency of these oscillations is a function of control voltage. </p>

**Schematic of VCO** </p>
![vco_full](https://github.com/himansh107/nitjsr_pll_130nm/assets/75253218/dfc0547b-6e6c-40c3-9134-7b91a8fe3fec)

<br>

**Simulated output of VCO** </p>

![vco_o](https://github.com/himansh107/nitjsr_pll_130nm/assets/75253218/e7d3f9d2-d2df-4e5d-bf41-2ba48ebf410d)
</p>

We can see that the frequency of oscillations increases with increase in control voltage input to VCO. </p>

### Frequency Divider 
</p>
The frequency divider used in this circuit divides the input frequency by 8 times. </p>

**Simulated output** </p>

![fd](https://github.com/himansh107/nitjsr_pll_130nm/assets/75253218/e941b29a-a736-4024-9de8-6e646025c8ad) </p>

## Full PLL Simulation  
</p>

**Integrated schematic** </p>
![pll](https://github.com/himansh107/nitjsr_pll_130nm/assets/75253218/abf8a8a8-83d6-4d39-a57c-2fb0914da0ff)

</p>

### Pre layout Simulation results
</p>

Generates 8x Multiplied Clock

<b> Pre-Layout: </b> <br>

Frequency Obtained for 5Mhz input: &nbsp;&nbsp;&nbsp;39.93MHz <br>
Frequency Obtained for 12.5Mhz input: &nbsp;&nbsp;&nbsp;100.5MHz

Duty Cycle obtained: &nbsp;&nbsp;&nbsp;66.65% at 40MHz and 65.92% at 100MHz

Lock-in starts at ~2.5us for 5MHz input and ~6us for 12.5Mhz input


**40Mhz output** 
</p>

![5mhz](https://github.com/himansh107/nitjsr_pll_130nm/assets/75253218/7389b432-c6e5-4f64-80cd-2831aa67e6b6)


</p>

**100Mhz output**
</p>

![12 5mhz](https://github.com/himansh107/nitjsr_pll_130nm/assets/75253218/63fd74bb-df1e-4dc5-8792-fa25ee5f8dec)

</p>

**Close up for 100Mhz** </p>
![12 5mhz closeup](https://github.com/himansh107/nitjsr_pll_130nm/assets/75253218/88a810dc-526c-47c8-9e8e-c0d69aa15353) </p>

**Red signal constantly overlaps blue signal indicating locked state.** </p>



<h3> References </h3>
<b>[1]</b> Sun, Qingbo et al. “On-chip Phase Locked Loop (PLL) design for clock multiplier in CMOS Monolithic Active Pixel Sensors (MAPS).” (2009). <br> <br> 
<b>[2]</b> Vincent Von Kaenel, “A 320 MHz, 1.5 mW @ 1.35 V CMOS PLL for Microprocessor Clock Generation” (1996) <br> <br> 
<b>[3]</b> J. Dhurga Devi “Jitter Reduced Self Biased PLLs—A Systematic Simulation Study”
<b>[4]</b> Jitter and Phase Noise in Ring Oscillators, Ali Hajimiri, Sotirios Limotyrakis, and Thomas H. Lee <br> <br> 
<b>[5]</b> Mo Zhang, A Programmable F ammable Frequency Divider Ha equency Divider Having a Wide Division Ratio ving a Wide Division Ratio Range, and Close-to-50% Output Duty-Cycle  <br> <br> 
<b>[6]</b> George Tom Varghese, MS thesis on “Phase Locked Loop Design as a Frequency Multiplier” NIT Rourkela (2009) <br> <br> 
<b>[7]</b> Yang Liu, “Phase Noise in CMOS Phase-Locked Loop Circuits” (2011) <br> <br> 
<b>[8]</b>	Rushabh Mehta, Design and implementation of a phase locked loop for high-speed serial links <br> <br> 
<b>[9]</b> Shruti Suman, An Improved Performance Ring VCO: Analysis and Design (2018)  <br> <br> 
<b>[10]</b> Scott Buchanan, Phase Locked Loop Integrated Circuit (2015)  <br> <br> 



