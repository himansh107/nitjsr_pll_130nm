# 130nm clock multiplier PLL

An on-chip clock mulitplier PLL designed using open source tools and sky130nm pdk

## Introduction
A clock multiplier Phase-Locked Loop (PLL) is a negative feedback control system that generates a high-frequency output clock whose phase is related to the low frequency input clock. The need for PLLs is widespread as they find application in analog, digital, RF and communication systems. <br>The low-frequency clock can be generated using a crystal with frequencies up to 200 MHz. Generating a high-frequency clock with high spectral purity is not possible with a crystal and this is where PLLs come into play. <br>

A simple PLL consists of a Phase and Frequency Detector (PFD), Charge Pump (CP), Loop Filter (LF), Voltage Controlled Oscillator (VCO) and a frequency divider in the feeback path <br>

This PLL design multiplies clock frequency by 8 times. 

## Significance of a PLL 

<img src ="https://github.com/himansh107/nitjsr_pll_130nm/assets/75253218/8809f5d2-4f9e-4b6b-918d-14ddeb37264a" width="750"/>

</p>The processors used in our smartphones have various modules inside serving different purposes. These modules may include CPU, GPU, RAM, WiFi module, Audio module, and so on. 

</p> The interesting thing is that each of these modules may not run at a particular clock frequency. For example, the CPU would need a high clock frequency for performing calculations faster, while the Display module may not.
</p> This is where a Phase-Locked Loop circuit comes into the picture. As shown in Figure 4, a PLL is installed at the clock entry point of each above module to produce the desired frequency at which the module would operate.
</p>

<br>

## Specifications

### Pre layout simulations

| Parameter | Description | min | val | max | Unit | Conditions |
| --- | --- | --- | --- | --- | --- | --- |
| VDD | Digital Supply | - | 1.8 | - | V | T = 27C |
| F<sub>CLKREF</sub> | Reference | 5 | 9 | 12.5 | MHz | T = 27C |
| F<sub>CLKOUT</sub> | Output Clock | 40.98 | 71.42 | 100 | MHz | PLL Mode, T = 27C |
| V<sub>ctrl</sub> | Control Voltage | 0.77 | 0.817 | 0.844 | MHz | PLL Mode, T = 27C |
| DC | Duty Cycle | 60.24 | 61.26 | 61.38 | % | T = 27C | 
| T<sub>SET</sub> | Settling Time | ~12 | ~11.5 | ~9 | us | T = 27C |


### Post layout simulations

| Parameter | Description | min | val | max | Unit | Conditions |
| --- | --- | --- | --- | --- | --- | --- |
| VDD | Digital Supply | - | 1.8 | - | V | T = 27C |
| F<sub>CLKREF</sub> | Reference | 5 | 9 | 12.5 | MHz | T = 27C |
| F<sub>CLKOUT</sub> | Output Clock | 40.76 | 73.26 | 100 | MHz | PLL Mode, T = 27C |
| V<sub>ctrl</sub> | Control Voltage | 0.732 | 0.780 | 0.789 | MHz | PLL Mode, T = 27C |
| DC | Duty Cycle | 56.82 | 58.82 | 60.27 | % | T = 27C | 
| T<sub>SET</sub> | Settling Time | ~8.6 | ~5.1 | ~6.4 | us | T = 27C |

## Block Diagram

![block diagram](https://github.com/himansh107/nitjsr_pll_130nm/assets/75253218/898a9849-547b-4b49-8693-218195f6f29f)
<br>

## EDA tools used

1. xschem
2. ngspice
3. magic vlsi 


## Pre layout simulations

 ### 1. Phase frequency detector (PFD)  </p>
 A phase frequency detector or a phase comparator outputs an UP or a DOWN signal depending upon the timed phase difference between the input signal and reference signal. If the input signal leads the reference signal an UP signal is produced whereas if the input lags the reference signal a down signal is produced. </p>
 
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
   <br>

![pfd_sim2](https://github.com/himansh107/nitjsr_pll_130nm/assets/75253218/358856e7-eb94-4cbf-899f-e3022d035a80)

Fig - Representation as a Phase error detector <br>

   - considering input and reference signal to have different time periods </p>

   ![pfd](https://github.com/himansh107/VSDCMOS_labs/assets/75253218/e8e84f63-12d9-4634-96e7-b8d72d29cae2)
</p>

In the above simulation result when **f_clk_in** **leads** **f_vco** , **UP** signal is triggered (yellow). While when the **f_clk_in lags f_vco** , **DOWN** signal is triggered. 
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

<img src ="https://github.com/himansh107/nitjsr_pll_130nm/assets/75253218/dbb0c2a0-bd57-489f-9b6d-69b2edfd0263" width="400"/>

</p>
This charge on the plates of the capacitor generates a voltage, which is then used as the control signal (Vctrl) to the VCO. </p>

**Design considerations of Loop filter**</p>
The loop filter consists of a resistor in series with a capacitor. This combination is in parallel with another capacitor as shown in figure above Capacitor C1 adds an integrator path and introduces a pole. The resistor adds a proportional path and introduces a zero. Thus, the system is stable with the addition of these two paths. Capacitor C2 is used to suppress the Vctrl ripple. However, the addition of an additional capacitor comprises on the stability of the PLL. This is because C2 adds another pole. So, to mitigate this issue the value of C2 is chosen to be smaller than C1 (~C2 = 0.1*C1) </p>

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

Frequency Obtained for 5Mhz input: &nbsp;&nbsp;&nbsp; 40.98MHz <br>
Frequency Obtained for 12.5Mhz input: &nbsp;&nbsp;&nbsp; 100MHz

Duty Cycle obtained: &nbsp;&nbsp;&nbsp; 60.24% at 40MHz and 61.38% at 100MHz

Lock-in starts at ~12us for 5MHz input and ~9us for 12.5Mhz input


**40Mhz output** 
</p>


![5mhz, 40 98mhz, 12us](https://github.com/himansh107/nitjsr_pll_130nm/assets/75253218/24ef39cf-3469-4555-af45-f196ab5a8252)




</p>

**100Mhz output**
</p>


![12 5mhz, 100mhz, 9us](https://github.com/himansh107/nitjsr_pll_130nm/assets/75253218/90e3611a-f67f-4c99-b4a6-323000cda41b)



</p>

**Close up for 100Mhz** </p>
![close up for 100mhz op](https://github.com/himansh107/nitjsr_pll_130nm/assets/75253218/51e7675e-48ff-4e51-bccb-4e483883e9e7)
 </p>

**Red signal constantly overlaps blue signal indicating locked state.** </p>

**Pre-layout summary** </p>

| Input Frequency | Output Frequency |
| :---:  | :-: |
|5MHz|40.98MHz|
|9MHz|71.42MHz|
|10MHz|80.97MHz|
|12.5MHz|100MHz|

## Post layout circuits

### a. Phase Frequency Detector

<img src ="https://github.com/himansh107/nitjsr_pll_130nm/assets/75253218/684e746d-58a7-4822-b41c-8c5ee2b6ede9" width="800"/>

</p>
Fig: Layout of Phase Frequency Detector (PFD)
</p>

### b. Voltage controlled oscillator

<img src ="https://github.com/himansh107/nitjsr_pll_130nm/assets/75253218/d85d276e-f237-4e7c-8b1c-b94341e762b9" width="800"/>

</p>
Fig: Layout of Voltage controlled oscillator (VCO)
</p>

### c. Charge pump & loop filter

<img src ="https://github.com/himansh107/nitjsr_pll_130nm/assets/75253218/49cd8b07-fd8e-49ed-9c59-ecec3e08b041" width="800"/>


</p>
Fig. - Combined layout of charge pump and loop filter (large boxes are the capacitors of the loop filter)
</p>

### Charge pump close up

<img src ="https://github.com/himansh107/nitjsr_pll_130nm/assets/75253218/e66b71e4-0240-4d74-abf7-530827c2e7a7" width="800"/>


### d. Frequency divider 

<img src ="https://github.com/himansh107/nitjsr_pll_130nm/assets/75253218/6ce6849d-b86a-48ac-a78b-eec360b9e60f" width="800"/>


</p>
Fig: Layout of Frequency divider by 8 circuit
</p>

The frequency divider by 8 is made from cascading three frequency divider by 2 circuits. The fd/2 ckt layout is shown below <br>

<img src ="https://github.com/himansh107/nitjsr_pll_130nm/assets/75253218/9154e348-9454-4047-9c02-a89535744014" width="800"/>


### d. Integrated PLL layout  

![pll](https://github.com/himansh107/nitjsr_pll_130nm/assets/75253218/c99e676d-f2f8-4045-8c3a-07cf45d05fe2)



</p> PLL Layout closeup </p>

![pll close up](https://github.com/himansh107/nitjsr_pll_130nm/assets/75253218/d45a6e15-418b-4197-bf0c-b9a4a0cefefb)


## Post layout simulations

| Parameter | Description | min | val | max | Unit | Conditions |
| --- | --- | --- | --- | --- | --- | --- |
| VDD | Digital Supply | - | 1.8 | - | V | T = 27C |
| F<sub>CLKREF</sub> | Reference | 5 | 9 | 12.5 | MHz | T = 27C |
| F<sub>CLKOUT</sub> | Output Clock | 40.76 | 73.26 | 100 | MHz | PLL Mode, T = 27C |
| V<sub>ctrl</sub> | Control Voltage | 0.732 | 0.780 | 0.789 | MHz | PLL Mode, T = 27C |
| DC | Duty Cycle | 56.82 | 58.82 | 60.27 | % | T = 27C | 
| T<sub>SET</sub> | Settling Time | ~8.6 | ~5.1 | ~6.4 | us | T = 27C |


### 1. 5 Mhz input 

![5mhz](https://github.com/himansh107/nitjsr_pll_130nm/assets/75253218/8b283b03-5bc5-4339-a76b-471f6559c6b6)

</p>

**Fig - PLL simulation for 5Mhz input frequency**
</p>

Red - input signal <br>
Blue (on red signal) - feedback signal <br>
Yellow - up signal <br>
Green - down signal <br>
Pink - control voltage <br>
Brown - output signal <br>

 <br>
 
![5mhz io](https://github.com/himansh107/nitjsr_pll_130nm/assets/75253218/26d17467-b601-428d-8a17-177750a49487)


</p>

**Fig - input - output for 5 Mhz operation**
</p>

 <br>
 
![5mhz close up](https://github.com/himansh107/nitjsr_pll_130nm/assets/75253218/02d512ad-2b7c-49a8-83ec-25f60750f3ce)

</p>

**Fig - settled output for 5 Mhz operation**
</p>
 <br>
 
### 2. 9 Mhz input 

![9mhz ](https://github.com/himansh107/nitjsr_pll_130nm/assets/75253218/32562197-f69e-4f4b-b44a-f2261fe4645d)

</p>

**Fig - PLL simulation for 9Mhz input frequency**
</p>
 <br>
 
![9mhz io](https://github.com/himansh107/nitjsr_pll_130nm/assets/75253218/ce9e5ad2-591e-4678-b524-f9d7468729a9)

</p>

**Fig - input-output for 9mhz operation**
</p>
 <br>
 
![9mhz close up](https://github.com/himansh107/nitjsr_pll_130nm/assets/75253218/a52abebe-0132-4ff4-b873-10ec05864b58)

</p>

**Fig - settled output for 5 Mhz operation**
</p>
 <br>
 
### 2. 12.5 Mhz input 

![12 5mhz ](https://github.com/himansh107/nitjsr_pll_130nm/assets/75253218/28ec026d-b424-4511-b959-4b16f7053330)
</p>

**Fig - PLL simulation for 12.5Mhz input frequency**
</p>
 <br>
 
![12 5mhz io](https://github.com/himansh107/nitjsr_pll_130nm/assets/75253218/ac5e35e0-a25b-4e6e-9580-c4542817cd69)

</p>

**Fig - input-output for 12.5mhz operation**
</p>
 <br>
 
![9mhz close up](https://github.com/himansh107/nitjsr_pll_130nm/assets/75253218/3f65067a-3c7c-4b79-a38c-324df48e4c99)

</p>

**Fig - settled output for 12.5 Mhz operation**
</p>
 <br>

## To use this repository

1.	Clone the github repository in your ubuntu system
2.	Go to postlayout folder and open terminal in the folder
3.	Type ‘ngspice pll_tb.spice’
4.	Paste the following command after simulation is complete

 ```bash
  plot V(f_clk_in)+8 V(x1.pfd_lay_0/f_vco)+8 V(x1.cp_0/up)+6 V(x1.cp_0/down)+4 V(x1.cp_0/vctrl)+2 V(f_clk_out)
```

 This will show the plots mentioned in the post layout section of this github repository <br>

**To see the layout**

1. Type in folder terminal window <br>


   ```bash
	magic pll.mag 
   ```


2. The layout will be hollow when the magic tool is opened for the first time. To see the layout clearly. Open the magic terminal (opens with the magic tool) and type

    ```bash
     % select top cell
     % expand
    ```

**NOTE** : all the above steps are valid for a system which has ngspice and magic (sky130nm integrated) installed. The same can be installed from [here](https://www.youtube.com/watch?v=VCuyO7Chvc8&t=2405s&ab_channel=whyRD) 


<h3> References </h3>
<b>[1]</b> Paras Gidd, avsdpll_3v3 github repository <br> <br> 
<b>[2]</b> Sun, Qingbo et al. “On-chip Phase Locked Loop (PLL) design for clock multiplier in CMOS Monolithic Active Pixel Sensors (MAPS).” (2009). <br> <br> 
<b>[3]</b> Vincent Von Kaenel, “A 320 MHz, 1.5 mW @ 1.35 V CMOS PLL for Microprocessor Clock Generation” (1996) <br> <br> 
<b>[4]</b> J. Dhurga Devi “Jitter Reduced Self Biased PLLs—A Systematic Simulation Study”
<b>[5]</b> Jitter and Phase Noise in Ring Oscillators, Ali Hajimiri, Sotirios Limotyrakis, and Thomas H. Lee <br> <br> 
<b>[6]</b> Mo Zhang, A Programmable F ammable Frequency Divider Ha equency Divider Having a Wide Division Ratio ving a Wide Division Ratio Range, and Close-to-50% Output Duty-Cycle  <br> <br> 
<b>[7]</b> George Tom Varghese, MS thesis on “Phase Locked Loop Design as a Frequency Multiplier” NIT Rourkela (2009) <br> <br> 
<b>[8]</b> Yang Liu, “Phase Noise in CMOS Phase-Locked Loop Circuits” (2011) <br> <br> 
<b>[9]</b>	Rushabh Mehta, Design and implementation of a phase locked loop for high-speed serial links <br> <br> 
<b>[10]</b> Shruti Suman, An Improved Performance Ring VCO: Analysis and Design (2018)  <br> <br> 
<b>[11]</b> Scott Buchanan, Phase Locked Loop Integrated Circuit (2015)  <br> <br> 


<h3> Acknowledgements </h3>

- I thank Mr. Kunal Ghosh, co-founder [VSD](https://www.vlsisystemdesign.com/), for helping me through out the project, I would like to thank my college professors Dr. Chandradeep Singh and Dr. Kunal Singh for encouraging me initiate this project and helping me with conceptual doubts.
- I thank Stephan Scrippers, David Mitchell Bailey, Luis Henrique Rodovalho, Tim Edwards and Lucas Daudt Franck from the [sky130 Slack channel](https://join.slack.com/t/open-source-silicon/shared_invite/zt-2eg56th1v-2SkdbmD6cGr9EbAFCJgIXw) for helping me with the doubts in xschem and skywater pdk.
- Rajdeep Mazumder, who's [VLSI Project help video](https://www.youtube.com/watch?v=VCuyO7Chvc8&t=2405s&ab_channel=whyRD) helped me install the open source tools
- I would also thank Paras Gidd, who's PLL repository was a reference for this project.


<h3> Contact Information</h3>

* Himanshu Singh (Author), BTech EE - himanshuksingh107@gmail.com
* Kunal Ghosh, Co-founder, VSD Corp. Pvt. Ltd. - kunalghosh@gmail.com

