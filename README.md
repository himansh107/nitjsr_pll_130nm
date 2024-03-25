# Clock mulitplier PLL 130nm 
Design of on chip clock multiplier PLL using sky130nm library file

## Introduction
A clock multiplier Phase-Locked Loop (PLL) is a negative feedback control system that generates a high-frequency output clock whose phase is related to the low frequency input clock. The need for PLLs is widespread as they find application in analog, digital, RF and communication systems. <br>The low-frequency clock can be generated using a crystal with frequencies up to 200 MHz. Generating a high-frequency clock with high spectral purity is not possible with a crystal and this is where PLLs come into play. <br>

A simple PLL consists of a Phase and Frequency Detector (PFD), Charge Pump (CP), Loop Filter (LF), Voltage Controlled Oscillator (VCO) and a frequency divider in the feeback path <br>

## Intended block diagram 
![Blank diagram](https://github.com/himansh107/VSDCMOS_labs/assets/75253218/b4aa826b-ffd5-4b25-a903-095417aef5d9)

## EDA tools used

1. xschem
2. ngspice

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
![Screenshot 2024-03-20 115846](https://github.com/himansh107/VSDCMOS_labs/assets/75253218/c0a05217-c34a-4d3c-b5d2-017c7602196d)
</p>
This charge on the plates of the capacitor generates a voltage, which is then used as the control signal (Vctrl) to the VCO. </p>

**Design considerations of Loop filter**</p>
The loop filter consists of a resistor in series with a capacitor. This combination is in parallel with another capacitor as shown in figure above Capacitor C1 adds an integrator path and introduces a pole. The resistor adds a proportional path and introduces a zero. Thus, the system is stable with the addition of these two paths. Capacitor C2 is used to suppress the Vctrl ripple. However, the addition of an additional capacitor comprises on the stability of the PLL. This is because C2 adds another pole. So, to mitigate this issue the value of C2 is chosen to be smaller than C1. </p>

**Expected output (CP+LF)** </p>
![image](https://github.com/himansh107/VSDCMOS_labs/assets/75253218/17e7cefc-83dc-4d28-8b2f-a3c657a7504e)

**Simulated output (CP + LF)** </p>

![Screenshot 2024-03-20 120732](https://github.com/himansh107/VSDCMOS_labs/assets/75253218/8550002a-a493-4e47-b917-310930bcb313)
</p>

 - NOTE - The above simulated output is of the Vctrl signal produced as a consequence of UP signal. As of now the output is errorneous. Reason being the voltage swing is from 1.79310V to 1.79390 V i.e.only 0.8mV. Upon plotting for long duration the plot would turn out to be a straight line.

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
<h3> Acknowledgements </h3>

- I thank Mr. Kunal Ghosh, co-founder [VSD](https://www.vlsisystemdesign.com/), for helping me through out the project, I would like to thank my college professors Dr. Chandradeep Singh and Dr. Kunal Singh for encouraging me initiate this project and helping me with conceptual doubts.
- I thank Stephan Scrippers for helping me with the doubts in xschem and Google, Skywater for the pdk.
- I would also thank Paras Gidd, who's PLL repository was a constant reference for this project.


<h3> Contact </h3>

* Himanshu Singh (Author), BTech EE - himanshuksingh107@gmail.com
* Kunal Ghosh, Co-founder, VSD Corp. Pvt. Ltd. - kunalghosh@gmail.com


