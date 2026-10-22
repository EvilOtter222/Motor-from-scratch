### Journal for 10/20 to 10/27

---

## October 20, 2026: <Ideas and Intial Planning>


# What should I build? 
I was recently scrolling on youtube, when I saw a few shorts about cyclodial disks, and a SimpleFOC library that controlled a gimbal motor. I love doing things from scratch--so the question was... what is the most powerful, versitile, and inexpensive robotic arm can I from scratch as possible. 

A normal robotic arm will be boring. It's mainly just cad-ing an arm design, buying a few servos, and hooking it up all to an arduino. I've done that before, there's a ton of tutorials for that, and that will be unoriginal, but my project will try to elevate what I've already done. 

The idea is to CAD a cycloidal disk, make my own motor controller by PCB-ing my own circuit board, and attempt to put this all together into a 3-DOF robotic arm, inspired by the LeRobot SO101 arm but with more control. If I can't finish everything for fabricate, I'll continue this project in phantom and 

# A few goals for this project
- Get MUCH better at journalling. In previous hack club contests (and just in general), I wasn't consistant, nor were my journals good quality. They were either lazy ("bug fixed", "wiring mistake fixed", etc. ), a modification of an AI generated summary, or just nonexistant. That's why I'm doing journal instead of lapse. Also with lapse I can larp work while with journals I actually have to accoplish something and make clear goals to get my hours turned into credits. 
- Continue off of the week 2 halflife idea that I dropped midway. I attempted the cycloidal disk from week 2 of halflife, but I didn't get far (a few defective parts were made in solidworks) and that's about it. I had basically no plan, so I'm going to make sure I emphasize preplanning. 
- Build my skills in solidworks and KiCad This will be the most ambitious project I will attempt, so hopefully I'll know more about kinematics and power in general! 
- Get better sticking to long term projects: hopefully I won't drop this 

# A few things I was debating
- Does this mix well. Like a recipie, I want to make sure everything can combine, and in my brain they don't contridict each other. The 
- How much should I make from scratch? If I wind the motors using copper myself, it would be harder, and it'll probably be worse because the quality of a motor is largely determined by precision, and my homebrew motor will lack that. 

# Part lists: 
GM4108, with enough torque, widely used, and 3 phases for good accuracy. The more phases a motor has, the greater the precision. 

7Semi DRV8316. Has all the basic functionalities for my 3-phase brushless motor, as well as voltage sensing, very compact as well. https://www.ti.com/product/DRV8316. 

STM microcontroller STM32G431 for the CAN bus, which rp2040 and esp chips only have TX/RX. I plan each motor module will have a motor, so TX/RX chainig is going to be annoying. Plus, the STM microcontroller is famous for being the fastest (which is important in this case)
  
# Extra Info
What I'm getting myself into. Here are some concepts I picked up. 

Brushed motors =  carbon brushes and communicator to transfer electricity
Brushless = Uses a chip and magnets 

Servo vs motor: Motor turns electricity into rotation, servo is just a motor with a sensor that measures position, and can go to a target position and correct itself. I'm turning a motor into a servo. 

Examples: 
MG90S - It uses PWM and a potentiometer to determine position
STS3215 - (LeRobot SO101 Servo) - 12-bit magnetic encoder for a full 360, what I'll try to replicate

I want to have an ecoder similar to the STS3215. 
Diametric magnet = north and south poles on opposite halves of the circle. 
Encoder = sensor that measures angular position and rotation. It uses fields from the diametric magnet? It apparently uses electromagnet waves? Force fields?? Still confused about that, will research further later. 

Main sources: 
https://humanoid.guide/humanoid-actuator-commonality/
https://www.youtube.com/watch?v=dv2URKU5YNU
https://robocraze.com/blogs/post/what-is-the-difference-between-servo-motor-vs-dc-motor?srsltid=AU7gw4WCsbc7nGbg8jf5Pzi3d1kVQT4udaOx4R-dlQe-FKettV7WRDk1
https://simplefoc.com/
https://www.st.com/en/evaluation-tools/b-g431b-esc1.html
https://oscarliang.com/brushed-vs-brushless-motor/

# General Sequencing: 
- Build the schematic + pcb first
- Build the cyclodial disk 
- Build an enclosure around the two parts
- Assemble the robotic arm

# Next Steps
- Look into the schematic to see what more specific parts I'll need



Easier said than done. 

# Copyright
Images are getting difficult to find because of copyright :(. Don't worry, I'll redraw the diagrams in the future. 
# AI use: 
Google AI for researching, Claude for suggesting the STM microcontroller for the CAN lines instead of TX/RX chaining. 
**Total time spent: 3h**


## October 21, 2026 <Selecting parts>

Yesterday, I figured out the general parts. But to run a CAN bus, we need more for the STM, we need a crystal more precise CAN bus. 

## Crystals  
Here's the different kinds of crystals I found out about researching (from least to most accurate):
1. Default crystal . 
2. regular crystal 
3. TCOX 
4. OCXO 
5. Double-oven OCXO and BVA

Shown from this document: https://www.nxp.com/docs/en/application-note/AN1798.pdf in example 4.2  (8mhz crystal and have the bit rate of 1mbps):

Default crystal has a ppm of +- 1%, which isn't as accurate enough. 


The regular crystal has a ppm of 20
8,000,000 * 20/1,000,000 = +/- 160Hz.

Thus, the regular crystal is more than enough. 

But kind of crystal should I get? 

The second optimization is a lower pF crystal, as it'll be faster, and require less power, as well as giving the STM more margin. 

I was initally thinking about the ECS-80-12-33-JGN-TR3, but I forgot about the ESR (internal resistive energy loss). The ESR was 400, while I found a better alternative: 

The Abaracon ABLS 8.000 MHz, and although electrically it's very good, 11.4 x 4.7mm might be too big. 

Wait, I'm not sure where I got stuck on 8MHz, so I decided on the https://www.lcsc.com/product-detail/SMD-Crystals_Yangxing-Tech-X322516MMB4SI_C112972.html, which is both small, 12 pF, and I can change the code to change the Hz (the code can only run in 8Mhz). This is what I'll be using!! 

## CAN communication
Talking about all this CAN, I need a good CAN transciever to conver the TX and RX bits into high and low voltages. First search result I saw was the SN65HVD230DR, but good nonetheless. Only thing is it's rated for 1 Mbps, which isn't the fastest CAN can go. 

The TCAN3413 looks a bit better because it's smaller and it has 8Mbps.  It's smaller as well. 
https://www.ti.com/product/TCAN3413

This video I watched was very help: https://www.google.com/url?sa=i&source=web&rct=j&url=https://www.youtube.com/watch?v%3DaFX8asQy4kE%26t%3D627&ved=2ahUKEwiR94vqncyXAxUoM1kFHadzHMUQ0YISegoIAggACAAIEBAQ&opi=89978449&cd&psig=AOvVaw2ifDe8YE5ZC9qpvRfCtFrF&ust=1792710677257000. 

It looks like I need a termination resistor, decoulpling capacitor, and a bus connector. 
It would be nice to have  TVS diode and a common mode choke, as well as a debug setup. 

Here's what my schematic looks like so far. I finished the crystals and added the 120ohm resistor for the end of the CAN bus: 

