---
layout: page
title: Custom Discrete 25W Buck Converter
description: Current-Controlled 
img: assets/img/418_buck_converter_pic.jpg
importance: 1
category: electronics
related_publications: true
---

## Premise + Spec
In the automotive industry, the push for enhanced fuel efficiency and reduced
emissions has led to the widespread adoption of stop/start technologies,
particularly in the North American (NAFTA) market. These systems, which
automatically turn off the engine when the vehicle is stationary, present a
significant challenge during the restart process, commonly referred to as "warm
crank." 

During this phase, the sudden load fluctuations can cause detrimental
effects on the vehicle's electrical system, including voltage drops that may result in flickering lights and compromised functionality of essential features like the radio and HVAC systems. 

To address these issues, this project aims to develop a DC-to-DC converter designed to act as a Voltage Stabilization Module (VSM).


<div class="row">
    <div class="col-sm-2 mt-3 mt-md-0">
    </div>
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/418_project_specs.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-2 mt-3 mt-md-0">
    </div>
</div>

## Open Loop Modelling + LC Sizing + Loss Estimation
To model the LED, we read off the forward voltage vs. current graph from the datasheet to get 18V and 2.5 Ohms (the slope of the I-V curve at ambient temp).

We used ideal inductor, capacitor, and rough switch models. 

Of course losses depend on how you model the components, but we are calculating loss simply here to get a ballpark estimate for efficiency and thermal management.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/418_losses.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

<div class="row">
    <div class="col-sm-3 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/lc_sizing_equations.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-4 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/418_buck_converter_lc_sizing.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-5 mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/418_sim.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

## Schematic Design + Layout
High and low side gate drivers were designed in accordance with the datasheet, as was 555 timer and the comparator.

The Potentiometer in the control circuit changes the 5V reference voltage between 0.1V and 1V which sets the output current between 0.1A and 1A. It was sized with a voltage divider.

There are several pull up resistors to ensure proper voltage levels.

The professor restricted us to just THT components and no polygons, so the layout is a bit simplistic.
<div class="row">
    <div class="col-sm-3 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/418_sawtooth.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-3 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/418_control_circuit.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-3 mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/418_buck_converter_power_circuit.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-3 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/418_buck_converter_layout.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

## Controller Design
I derived the plant transfer function Gid (steps shown below). I then arbitrarily chose a crossover frequency and hand-tuned the PI controller. I know that's not the most scientific way to do it, but I didn't know any better at the time. I also used the wrong Vm value of 15 instead of 5, which made my phase margin too high and my crossover frequency lower than it could have been. Even worse, I soldered on the wrong value capacitor, making my Ki low and killing my bandwidth.

Based on the Gid, the open loop system has two poles and one zero. The two poles are at w0 = 1/sqrt(LC) causes -40 dB/dec slope and -180 deg phase shift. There is a zero at 1/RC which adds +20 dB/dec slope and +90 degree phase shift. Thhe compensator adds a pole at the origin and zero at Ki/Kp. The pole at the origin adds +20 dB/dec and -90 deg phase shift. The zero adds +20 dB/dec and +90 deg phase shift.

The compensator pole and zero work far below the resonant frequency so the plant poles and zero dominate response after crossover.

Even still, the controller worked well for  0.1 to 1A load current. Just goes to show that we don't all need to be controls nerds to get the job done.

I've attached the closed loop step response and Bode plot with gain and phase margins below along with the MATLAB code I used .

<div class="row">
    <div class="col-sm-2 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/418_gid_part1.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-2 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/418_gid_part2.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-4 mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/418_bode_plots.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-4 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/418_step_response.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

<div style="max-width: 720px; margin: 0 auto 1rem; border: 1px solid #ddd; border-radius: 4px; background: #111; color: #eee; padding: 12px; overflow: auto; max-height: 260px; font-size: 13px;">
<pre style="margin: 0;"><code class="language-matlab">% clear all
close all

%Buck converter parameters
C=1e-6;
L=367e-6;
R=20.6;
Vg=24;
V=20.6;

D=V/Vg;              %Duty Cycle

w0=sqrt(1/L/C);      %Undamped Resonant Frequency
Q=R/sqrt(L/C);       %Quality Factor
Gd0=Vg;              %DC Gain of Duty Cycle Transfer Function
Gg0=D;

s=tf('s');

Zout=(1/R+1/s/L+s*C)^(-1);
Gid=((s*C*R+1)*(Vg/R))/(L*C*s^2 + L*s/R + 1);
%% Uncompensated Loop gain plot

%Control parameters
H=0.1; %aka Gm, basically the resistance of your sense resistor
Vm=5; %peak of your ramp (sawtooth)

disp('Proportional Integral Control')

Kp = 17;
Ki = 8e5;
Gc=Kp+Ki/s

figure
bode(Gc,{0.1,1e5})
grid

disp('Loop Gain Transfer Function G_loop (s)')
Gloop=Gid/Vm*Gc*H;
figure
margin(Gloop,{0.1,1e5})
grid

figure
step(Gloop/(Gloop+1))
title('Closed Loop Step Response')
grid on


% wc = w0/5;                          % desired crossover frequency (rad/s)
% magGid = abs(freqresp(Gid,j*wc));  % plant magnitude at wc
% Kp = Vm / H / magGid;              % proportional gain for unity loop gain
% omega_i = wc/10;                   % PI zero 10x below crossover
% Ki = Kp * omega_i;                  % integral gain</code></pre>
</div>

## Results
Inductor Current Ripple: 30mA (2.6%)

LED Current Ripple: 8.7mA (0.9%)

Output Voltage Ripple: 115mV (0.6%)

Transient Response: 3.2ms

Efficiency: 94% 
<div class="row">
    <div class="col-sm-4 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/418_inductor_ripple.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-4 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/418_LED_current_ripple.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-4 mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/418_output_voltage_ripple.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="row">
    <div class="col-sm-4 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/418_transient.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-4 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/418_efficiency.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-3 mt-3 mt-md-0">
        <div style="text-align: center;">
            <iframe width="360" height="215" src="https://www.youtube.com/embed/vKDa1ZlMXtw?si=Wy8alJovNj_asfXS" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
        </div>
    </div>
</div>

## Pain Points
Honestly a majority of the pain was just in manufacturing. The input capacitor was not big enough and PSU ripple + cable parasitics made the measurements noisy. Twisted wire pairs would be good here and following these rules for input cap sizing. The noise here propagated through the system and killed controller performance. It throttled Vref because of excessive noise on power side and noise coupling into ground loops all around. 

I also soldered the wrong capacitor in the controller, which made my bandwidth much lower than expected. I realized that based on visual inspection. In the end I left it in since the performance was still acceptable.

Equipment failures were common like faulty oscilloscope probes

We had a minor issue where we used the output pin of the 555 timer instead of the discharge pin, but we caught that early on and fixed it by breaking the lead and added solder to the correct pin.

## Future Improvements
Use a controller IC! LM5116 by TI works well here. Also make it synchronous for higher efficiency and lower conduction losses. Also use SMT components for smaller form factor + 4 layer PCB for better noise immunity as we can have two internal ground planes or one ground and one power plane.

Model circuit in Simulink if the load is sensitive or we need a hyper-precise controller. I did this in my motor controls class. Motors and dynos are expensive. We have complex non-linear models in simulink of switches and load so we can simulate behaviour in software first. In this buck converter, the code simulation of step response was enough.

## Layout Tips
Place the input decoupling capacitors, high-side switch, low-side switch/diode, and return (ground) as close together as possible with wide, short traces or copper pours.

Why:The switching loop carries large fast pulsed currents. Minimizing loop area reduces parasitic inductance, lowers voltage overshoot and ringing (V = L·di/dt), and cuts EMI and switching noise

On the control side, keep feedback traces away from inductor and switches and keep everything compact to reduce noise pickup.
Input capacitor needs to be close to the input cables.
Give switches and inductors large copper areas for heat dissipation or thermal vias.

Keep a power ground and analog ground separate to minimize noise coupling. Star ground at one point. Low impedance return path for signals and shields from EMI.

Keep power side on top layer and control side on bottom layer. Use a kelvin sense resistor and route a diff pair to the controller.
## Design Tradeoffs
component sizing: inductor and capacitor size tradeoffs with switching frequencies and ripple. Larger L and C reduce ripple but increase size and cost. Higher switching frequencies allow smaller L and C but increase switching losses and EMI. Find a balance based on application requirements. 

Decide spec choices like ripple current, output voltage ripple, transient response, and efficiency based on application needs. For example, LED drivers may prioritize low current ripple for consistent brightness, while battery chargers may focus on efficiency to minimize heat generation.

Digital or Analog Control: Digital is more flexible and easier to tune, but analog is lower cost and lower power consumption. Always use analog for fast switching convertert. Digital is more for slow systems like solar MPPT or battery management.

CCM vs DCM: 
Use DCM when control simplicity or light-load efficiency matters more than conduction loss. The plant is first order so easy to control voltage and no RHP Zero (seen in boost/flyback). In light loads, switching loss dominates so DCM decreases this.

## System Integration Notes
Modelling in full system. 

Protect nearby digital signals.

