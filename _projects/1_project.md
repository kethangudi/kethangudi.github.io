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

## Open Loop Modelling + LC Sizing
To model the LED, we read off the forward voltage vs. current graph from the datasheet to get 18V and 2.5 Ohms (the slope of the I-V curve at ambient temp).

We used ideal inductor, capacitor, and rough switch models. 

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
<div class="row">
    <div class="col-sm-8 mt-3 mt-md-0">
        The professor restricted us to just THT components and no polygons, so the layout is a bit simplistic.
    </div>
    <div class="col-sm-4 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/418_buck_converter_layout.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

## Controller Design
add simulink code and results
hand calculations for compensator design
where are your poles and zeros placed and why?

## Results
add graphs of results

## Pain Points
add chart + some notes from debug doc

## Future Improvements
controller IC + make it syncrhonous

SMT components for smaller form factor

4 layer PCB

LDO's to supply reference voltages

How to supply -15V for the controller?

Model controller in Simulink

## Design Tradeoffs
component sizing

switching frequencies

spec choices like ripple current, output voltage ripple, transient response

are there design tradeoffs in the controller design?

## System Integration Notes
Modelling in full system.

Protect nearby digital signals.

Thermal management.

<div class="row">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/3.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Caption photos easily. On the left, a road goes through a tunnel. Middle, leaves artistically fall in a hipster photoshoot. Right, in another hipster photoshoot, a lumberjack grasps a handful of pine needles.
</div>
<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/5.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    This image can also have a caption. It's like magic.
</div>

You can also put regular text between your rows of images, even citations {% cite einstein1950meaning %}.
Say you wanted to write a bit about your project before you posted the rest of the images.
You describe how you toiled, sweated, _bled_ for your project, and then... you reveal its glory in the next row of images.

<div class="row justify-content-sm-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/6.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-4 mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/11.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    You can also have artistically styled 2/3 + 1/3 images, like these.
</div>

The code is simple.
Just wrap your images with `<div class="col-sm">` and place them inside `<div class="row">` (read more about the <a href="https://getbootstrap.com/docs/4.4/layout/grid/">Bootstrap Grid</a> system).
To make images responsive, add `img-fluid` class to each; for rounded corners and shadows use `rounded` and `z-depth-1` classes.
Here's the code for the last row of images above:

{% raw %}

```html
<div class="row justify-content-sm-center">
  <div class="col-sm-8 mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/6.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
  </div>
  <div class="col-sm-4 mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/11.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
```

{% endraw %}
