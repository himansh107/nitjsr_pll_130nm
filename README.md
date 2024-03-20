# 180nm Clock mulitplier PLL 
Implementation of on chip clock multiplier PLL using SCL 180nm library file

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
There is a 1ns difference between the time period of f_vco and f_clk_in. This is done to emulate phase & frequency error. 
  </p>
  
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

![Screenshot 2024-03-20 120732](https://github.com/himansh107/himanshu_pll_nitj/assets/75253218/2ae63ff7-bca2-4c0f-9ef0-c7915679537a)
</p>

 - NOTE - The above simulated output is of the Vctrl signal produced as a consequence of UP signal. As of now the output is errorneous. Reason being the voltage swing is from 1.79310V to 1.79390 V i.e.only 0.8mV. Upon plotting for long duration the plot would turn out to be a straight line.


