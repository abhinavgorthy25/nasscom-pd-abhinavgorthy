
# NASSCOM Digital VLSI SoC Design and Planning 


I discovered this workshop on LinkedIn through Kunal Ghosh. It’s a two-week Digital SoC and Physical Design course using Open Lane, a platform that employs open-source tools to create digital designs from RTL to GDSII. The workshop utilizes the SkyWater 130 open-source PDK.

## Aim of the Workshop 
The workshop focuses on the physical design of a macro CPU core called picorv32a, a part of the VSD Squadron developed by VLSI System Design (VSD). Based on the RISC-V architecture, this development board is similar to Arduino Uno boards and suitable for home automation, IoT, and more applications.

### Image of VSDSQUADRON (Source: VLSI System Design)

![Key-features-1](https://github.com/user-attachments/assets/091fd613-7d83-4de1-aac2-88522fd75f3a)


### Overview of Chip Making 


<img width="990" alt="2" src="https://github.com/user-attachments/assets/a68bb643-8976-4ad7-840b-d838616bcbc6">


### Agenda of the workshop 
![3 ](https://github.com/user-attachments/assets/c8401cac-ad92-4ba0-870b-f062a96499c9)



## Day 1: Inception of open-source EDA, OpenLane and Sky130 PDK 

### SOC: System on Chip 

- SoC is an Integrated Circuit consisting of all the key components like Memory, Logic, and Cache on a single IC, decreasing the system area and improving the area and power.
- Architecture is crucial for a SoC or any other ASIC. Most of the SOCs follow ARM architecture while the traditional computers follow x86 and x64 architecture which are proprietary, and licenses are required to purchase them. In this workshop we focused on an open-source ISA (Instruction Set Architecture) called RISC -V which was developed by UC Berkeley in 2010 and became famous among the researchers as an industry expert. It is one of the fastest-growing ecosystems and uses smaller instruction sets for better computation and efficiency in terms of Performance, Power, and Area.

### Foundry Intellectual Property (IPs):

- Few of the components in an SoC need some assistance and constant interaction with the semiconductor fabrication unit or foundry and those are termed as Foundry IPS (Example: SRAM, PLL, ADC, and DAC)
- IPs can be licensed from IP vendors or developed in-house (Example: Micron -DRAM)
- They provide a level of abstraction that allows designers to focus on higher-level design hiding the internal information.

### Macros 
- Components that are reusable and classified into three main categories soft macro, firm macro, and hard macro.
- Soft Macro: Synthesizable, flexible and editable
- Firm Macro: Have a Netlist Format, portable, and have predictive PPA. 
- Hard Macro: Same as Foundry IPs and has optimized PPA and timing
#### Chip Design 
![Picture 4 png](https://github.com/user-attachments/assets/f7b460e2-7551-4f18-b9d4-83a0dc64130e)


### Software and Hardware Integration 

All the applications interact with the ICs and perform tasks as per the user requirements. The system software is responsible for translating the application program into binary language, which the hardware can understand and execute. The three main layers of the system software are. 
#### 1) Operating System (OS): MacOS, Windows, Linux
- Handle IO Operations 
- Process and Memory Management
- Interrupt Handling 
 #### 2) Compiler (Gcc, Clang) 
- Converts the high-level language into an executable file in assembly-level instructions and later sent to an Assembler.
-  Different architectures have their own instruction sets, and the compiler ensures that the instructions generated are compatible with the targeted architecture.

#### 3) Assembler
- It converts the assembly-level language into binary code, which is a sequence of 0s and 1s that can be directly dumped into the hardware.
- Hardware Descriptive Languages like (Verilog, VHDL) also interact with assembler
  
![Picture 5 ](https://github.com/user-attachments/assets/a82ba663-45b4-40af-952e-ff4585da4e79)

### Open-Source Digital ASIC Design
#### Three pillars of ASIC DESIGN 
  ![Picture 6 ](https://github.com/user-attachments/assets/5ee5a992-4b8e-43e0-889f-5da4215429a7)

#### 1)  RTL Design: Register Transfer Level refers to pre-designed and pre-verified digital hardware components or blocks that are described at the Register Transfer Level (RTL). It is an HDL representation of a digital circuit or a portion of a circuit. 
#### 2)  EDA tools: Electronic Design Automation Tools are software applications that are used in design, simulation, analysis, automate the process of chip-making which increases efficiency and reduces time-to-market aspects.
#### PDKs: Process Design Kits are a set of files which are model files provided by the foundry that abide by all the rules of fabrication and are used by the circuit designers to develop analog, digital and mixed-signal designs. It acts as a bridge between semiconductor fabrication engineers and circuit designers. 

These three can have limited access and most of them have licenses. However, many designs, tools, and pdks are open-source, and we will be using these open-source tools and files to generate a cpu core. 

### OPENLANE EDA Tools (RTL to GDSII)  
 OpenLane is an automated RTL to GDSII flow based on several tools. It provides several custom scripts for design exploration and optimization. The flow performs all ASIC implementation steps from RTL all the way down to GDSII. Currently, it supports both the A and B variants of the sky130 PDK, and the C variant of the gf180mcu PDK.

GitHub Repo
``` bash 
 https://github.com/The-OpenROAD-Project/OpenLane
 ```
The advantage of OpenLane is it is user-friendly, and we can modify the behavior of the circuit with a single configuration file. 

#### OpenLane Architecture 

![7](https://github.com/user-attachments/assets/106b206a-e7ec-43e2-84e2-62e680f39bf7)


| S. No | Task                      | Purpose                                                                                                                                              | Open Source EDA Tool                                                                              |
|-------|---------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------|
| 1     | RTL Design                | The functional behavior of the digital circuits is described using a hardware description language (HDL) like VHDL or Verilog.                        | EDA playground                                                                                    |
| 2     | RTL Synthesis             | Converts the high-level RTL description into a gate-level netlist. This stage involves mapping the RTL code to a library of standard cells and optimizing the resulting gate-level representation for area, power, and timing. | yosys/abc - Perform RTL synthesis and technology mapping.                                        |
| 3     | Floor Planning            | Decides chip's die, core and IO pad area and determines the placement of preplaced cells.                                                           | 1. init_fp - Defines the core area for the macro as well as the rows. <br> 2. ioplace - Places the macro input and output ports <br> 3. tapcell - Inserts welltap and decap cells in the floorplan                                     |
| 4     | Power Planning            | Distribution of Vdd and Vss to all the components in the core.                                                                                        | pdngen - Generates Power Distribution Network                                                     |
| 5     | Placement                 | Assigning the physical coordinates to each gate-level cell on the chip's layout while minimizing wire length, optimizing signal delay, and satisfying design rules and constraints.            | 1. RePlace - Performs global placement. <br> 2. Resizer - Performs optional optimizations on the design. <br> 3. OpenDP - Performs detailed placement to legalize the globally placed components <br> 4. Magic Layout - To check the layout                                                             |
| 6     | Clock Tree Synthesis      | Constructing an optimized clock distribution network within an integrated circuit (IC) minimizing clock skew and achieving timing closure.            | TritonCTS - Synthesizes the clock distribution network (the clock tree)                           |
| 7     | Routing                   | Connects the gates and interconnects on the chip based on the placement information avoiding congestion.                                              | 1. FastRoute - Performs global routing. <br> 2. TritonRoute - Performs detailed routing                                                        |
| 8     | Static Timing Analysis    | Checks the timing constraints and performs setup, and hold analysis.                                                                                       | OpenSTA - Performs static timing analysis on the resulting netlist to generate timing reports     |
| 9     | Sign-off                  | Series of checks and simulations to confirm that the design is ready for fabrication and obtain desired functionality and PPA.                        | 1. Magic - Performs DRC Checks & Antenna Checks <br> 2. KLayout - Performs DRC Checks <br> 3. Netgen - Performs LVS Checks <br> 4. CVC - Performs Circuit Validity Checks                                                         |
| 10    | GDS II                    | Graphical Data Stream (Structure) represents the complete physical layout of the chip that contains the geometric information necessary for fabrication, including the shapes, layers, masks, and other relevant details. | Magic - Generates .cif file for fabrication                                        |

                                                
Installation of Linux on MacOS (Apple Silicon) 
- Oracle Virtual Box is not compatible with the latest Apple silicon chips of Apple (M Series) and thus I used UTM for MacOS to install Ubuntu.
- I followed the video to install the Ubuntu: 
``` bash  
https://youtu.be/O19mv1pe76M?feature=shared
``` 
- To install Openlane I followed the instructions mentioned in the git hub repo 

```bash
  https://github.com/AnupriyaKrishnamoorthy/NASSCOM-PD-ANU. 
```
- They mentioned to install the following dependencies and packages along with the PDKS listed below:
``` bash 
sudo apt-get update
sudo apt-get upgrade
sudo apt install -y build-essential python3 python3-venv python3-pip make git
``` 
- To interact with OpenLane Docker has to be installed and for that run the following commands :
``` bash 
sudo apt install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io
sudo docker run hello-world
``` 
- If the above command executes it means that the docker is successfully installed 
- Run some additional commands for global usage of docker 
 ``` bash 
sudo groupadd docker
sudo usermod -aG docker $USER
sudo reboot  
# Check for successful installation
sudo docker run hello-world 
``` 
- These are the steps to install the OpenLane: 
``` bash 
git clone https://github.com/The-OpenROAD-Project/OpenLane --recurse-submodules 
cd OpenLane
make
make test
cd $HOME/OpenLane/designs/ci
cp -r * ../
``` 
- After make and make test you see all the tools pass the basic test. 
### Openlane Open PDKs install  
- This link directs to the installation of Open PDKs 
 ``` bash 
 https://web02.gonzaga.edu/faculty/talarico/vlsi/openpdk.html
 ``` 
- Make sure you install magic, ngspice and other required tools directly using sudo apt install <tool_name> and run its corresponding make file or  gcc file.
- Make sure you copy the design file picorv32a into your Openlane/designs directory.
-  Copy The default config.tcl and then use config.tcl file from post_config.tcl folder when required.

### Lab Exercise: 
- Steps to run synthesis in OpenLane Interactive mode:
``` bash 
cd ~/OpenLane
make mount
./flow.tcl -interactive
package require openlane 0.9
prep -design picorv32a
run_synthesis
```

#### Synthesis Run on Openlane 

<img width="1470" alt="Synthesis run on Open Lane interactive" src="https://github.com/user-attachments/assets/3a9b8c61-eeb8-4185-b753-6311c88ada6e">

- To view the netlist, go to the runs and look into the results of the latest run that you performed. 
A sample command is shown here : 
``` bash 
cd /home/OpenLane/designs/picorv32a/runs/RUN_1/results/synthesis
vim picorv32a.v
``` 
- Reports can be viewed in the reports folder.
 - AREA_0 Report:

    <img width="738" alt="Area report " src="https://github.com/user-attachments/assets/15c74fa0-15fe-4099-a0f9-6e4ea383f7fb">

 - Synthesized Verilog file:

<img width="737" alt="Synthesized verilog file" src="https://github.com/user-attachments/assets/8d2a4181-d324-4bb5-8b4a-4fd477397dc2">


## Day 2: Good Floorplan Vs Bad Floorplan and Introduction to Library Cells

### Floor Planning and Power Planning Key Concepts: 
##### Defining the width and height of the cell

- 	Die Area: A small square area on which the chip is fabricated that of consists core area and IO pads.
- Core Area: The main area where standard cells, macros/IPs are placed. 
- 	Core Utilization Factor: (Area Occupied by Netlist / Total Area of the core)
- Aspect Ratio: Height/ Width of the Core.
####  Defining locations of Pre-placed Cells
 -  The pre-placed cells are typically IPs that have been defined before automated PnR (Place and Route)
- 	Circuit Bipartitioning occurs and is organized into blocks/modules and placed along with the pre-placed cells. 
  ####  Surround the pre-placed cells with decoupling capacitors.
- Decoupling capacitors are large capacitors that store electrical charge They act as reservoirs and can send the same voltage as the power supply to the circuit for a period. 
- Some pre-placed cells reside far away from the Power supply and to void  IR drop De Caps are used. 
#### Power Planning: 

-	It aims to supply power evenly to all the circuits and plan the distribution of power and ground connections to ensure proper functionality and performance of the chip. 
-  Due to internal parasitics, hazards (Harmful Glitches) like voltage droop and ground bounce occur when there are variations in the voltage levels of different GND points due to transient currents. 
- Mesh Distribution strategy is usually preferred in power planning.
##### Pin Placement :
- Proper Pins must be allocated according to the floor planning in such a way that the input and output signals are strong with equal power distribution to all the cells along with standard packaging aspects.
- Create a blockage ring in between the core and IO pads to avoid crosstalk.
 Placement
- After all these, Placement is performed based on algorithms that minimize wire length. 

 ### Lab Exercise: 
A simple command is required to perform the floorplanning 
 ``` bash 
run_floorplan
 ```
<img width="738" alt="Floor Planning Run " src="https://github.com/user-attachments/assets/6d7f71c5-5e74-4800-8b61-f2c3d2225cd5">

-  Floor Planning DEF file:

<img width="738" alt="Floor Planning  Def file" src="https://github.com/user-attachments/assets/d5d1339a-5464-4760-a5e3-11d0a785710a">


- To view floorplan the following command is required. Make sure you copy the tech file (sky130A.tech) file into the existing directory or you can copy the path of the technology file. Lef (Library Exchange Format) is read to view the layout 
``` bash 
$ cd /home /OpenLane/designs/picorv32a/runs/RUN_1/results/floorplan
$ magic -T sky130A.tech lef read ../../tmp/merged.nom.lef def read picorv32a.def &
```

<img width="1406" alt="13" src="https://github.com/user-attachments/assets/c63be086-d52e-4df5-9218-22c309ba569b">


- Many commands are used in the magic tkcon command window 
- One such example is selecting the area of the required portion and typing what and in this case, it shows two different metal layers that are used in the floorplanning.
  
<img width="1406" alt="14" src="https://github.com/user-attachments/assets/21e6730d-aafd-48a9-ab3a-89c7949bb12c">


Run Placement :
``` bash 
 run_placement
 ```
<img width="1394" alt="15" src="https://github.com/user-attachments/assets/aee3e0fb-341f-4782-a1d3-2954b052fd9b">


- To view the placement use the following commands 
 ``` bash 
 $  cd /home /OpenLane/designs/picorv32a/runs/RUN_1/results/placement
$ magic -T sky130A.tech lef read ../../tmp/merged.nom.lef def read picorv32a.def &
```
<img width="1401" alt="16" src="https://github.com/user-attachments/assets/82f0ccf6-3fa5-4f14-be71-963102e25183">


- To insert a custom cell design into the existing flow we need to install the following package 
``` bash 
git clone https://github.com/nickson-jose/vsdstdcelldesign.git
```
- To view the layout of a basic inverter 
 ``` bash 
 magic -T ./libs/sky130A.tech sky130_inv.mag &
 ``` 
 ### Cell Design and Characterization Flow 

A library is a file where all the information of a different cells.

Cell Design Flow Key Concepts for EDA tools: 

Inputs:
 - PDKs
- 	Design Rule Checks (DRC), Layout vs Schematic (LVS) 
- 	SPICE- Models, library & user-defined specs
Design Steps and Outputs 
-	Circuit design 
-	 Layout design & characterization: Using Euler’s path and stick diagram (LEF output)
- Extraction of parasitics, extraction of spice netlist (GDS II, .cir, .lib  output files)

Standard Cell Characterization Flow Key Concepts for Spice Deck:
- Read the models and tech file that have parameters, equations (device physics) 
-  Read extracted spice Netlist
-	Recognize the   behavior of the netlist. 
- Input the model file of the subcircuits
- Attach the required power supply and name the nodes.
- 	Apply inputs (PULSE, PWL).
-	Apply Capacitive Load.
- Provide necessary simulation commands in Spice Deck (.tran . dc) 

After all these 8 steps, the information is sent to a Software called GUNA which generates the timing, and noise power models for these files. We characterize Timing, Power and noise.

![Picture17png](https://github.com/user-attachments/assets/aee2da03-1ef7-42bf-92b4-97d61cc5416d) 

### Timing Characterization:  
| Concept          | Definition                                                                                 | Formula                                                   |
|------------------|--------------------------------------------------------------------------------------------|------------------------------------------------------------|
| Rise Time        | Time taken for output signal to reach from 20% of Vdd to 80% of Vdd.                        | time(slew_high_rise_thr) - time(slew_low_rise_thr)         |
| Fall Time        | Time taken for output signal to fall from 80% of Vdd to 20% of Vdd.                         | time(slew_high_fall_thr) - time(slew_low_fall_thr)         |
| Propagation Delay| The time difference between 50% Vdd of input and 50% Vdd of output.                         | time(out_thr) - time(in_thr)                               |


## Day 3: Design Library Cell Using Magic Layout and ngspice characterization.

IO placer: Even after running the placement we can change the parameters and observe the places. I observed the change in the ports when I changed the value of the IO placer which is not equidistant from each other as in the previous case.

- Spice Deck Order, Syntax and Semantics:

<img width="1389" alt="18" src="https://github.com/user-attachments/assets/03378646-305d-40e5-8c6e-875a0c3df969">



#### CMOS Inverter Robustness Checks for Spice Deck:
We can change the load capacitance, and width of pmos and nmos and compare the results. 
- One of the important parameters is the Switching Threshold (Vm) a point at which Vin = Vout and when both are in the Saturation region.

<img width="1337" alt="19" src="https://github.com/user-attachments/assets/376f5f9d-ce64-4520-ae57-fc58822a34a8">

### Lab exercise: 
In order to get the inverter (Custom) files we need to clone a repository.
``` bash 
git clone https://github.com/nickson-jose/vsdstdcelldesign.git
``` 
For viewing the layout 
``` bash 
magic -T ./libs/sky130A.tech sky130_inv.mag &

```
#### CMOS Inverter Layout 


<img width="1401" alt="20" src="https://github.com/user-attachments/assets/80e40535-4195-4862-9635-8de57af19513">


CMOS is called a Complementary Metal Oxide semiconductor, and it is the most widely used technology in digital ICs and is fabricated using 16 steps.
-  Substrate Preparation
-  N-Well Formation
-  P-Well Formation
-  Gate Oxide Deposition
-  Poly-Silicon Deposition
-  Poly-Silicon Masking and Etching
-  N-Well Masking and Implantation
- P-Well Masking and Implantation
- Source/Drain Implantation
- Gate Formation
- Source/Drain Masking and Etching
- Contact/Via Formation
- Metal Deposition
- Metal Masking and Etching
- Passivation Layer Deposition
- Final Testing and Packaging
#### Generation of Spice Model from Magic Layout 
In the tkcon window we need to convert the layout to spice file
``` bash 
extract all
ext2spice cthresh 0 rthresh 0 
ext2spice cthreshold 0 and rthresh 0
```
Ensure that the RC values are zero.
Modify the existing spice to this: 
``` bash 
* SPICE3 file created from sky130_inv.ext - technology: sky130A
.option scale=0.01u
.include ./libs/pshort.lib
.include ./libs/nshort.lib
//.subckt sky130_inv A Y VPWR VGND
M1000 Y A VGND VGND nshort_model.0 w=35 l=23
+  ad=1.44n pd=0.152m as=1.37n ps=0.148m
M1001 Y A VPWR VPWR pshort_model.0 w=37 l=23
+  ad=1.44n pd=0.152m as=1.52n ps=0.156m

VDD VPWR 0 3.3V
VSS VGND 0 0V
Va A VGND PULSE(0V 3.3V 0 0.1ns 0.1ns 2ns 4ns)
C0 A VPWR 0.0774f
C1 VPWR Y 0.117f
C2 A Y 0.0754f
C3 Y VGND 2f
C4 A VGND 0.45f
C5 VPWR VGND 0.781f
//.ends
.tran 1n 20n
.control
run
.endc
.end
``` 
Use the following command in the Ngspice Shell:
``` bash 
 ngspice sky130_inv.spice
 ```

<img width="1401" alt="21" src="https://github.com/user-attachments/assets/7b1592c7-0be7-4e00-9a19-11d3b413e721">

#### Transient Analysis
<img width="1401" alt="22" src="https://github.com/user-attachments/assets/6f8c8ec6-00fb-4a61-85ab-fdfc2ba5a0ef">

These are the values that I got for timing characterization 
| Checks           | Definition                                                                                 | Values      |
|------------------|--------------------------------------------------------------------------------------------|-------------|
| Rise Time        | Time taken for output signal to reach from 20% of Vdd to 80% of Vdd.                        | 0.035 ns    |
| Fall Time        | Time taken for output signal to fall from 80% of Vdd to 20% of Vdd.                         | 0.026 ns    |
| Propagation Delay| The time difference between 50% Vdd of input and 50% Vdd of output.                         | 0.003 ns    |


### Lab Exercise on DRC Check: 
The Open-source community will be updating constantly and different versions will be still under development and this exercise aim is to understand the Magic Layout DRC engine.
Download the package using the command: 
``` bash 
wget http://opencircuitdesign.com/open_pdks/archive/drc_tests.tgz
``` 
-  DRC check for poly.mag
DRC ERROR COUNT = 32

<img width="1390" alt="23" src="https://github.com/user-attachments/assets/1b704812-5d61-4c2a-80ee-dbfa51963aea">


- Edit the drc_tests file by adding the allpolynonres to update the design rule check  based on the Skywater 130 PDK design rules information from Open Circuit Design.

<img width="1401" alt="24" src="https://github.com/user-attachments/assets/111579a1-d14f-401a-a461-038e5b69074b">


- Similarly include DRC line  for the npres as above: 

``` bash 
spacing npres allpolynonres 480 touching_illegal \
                    "poly.resistor spacing to N-tap < %d (poly.9)" 


``` 
- We can observe the increase in the DRC count which indicates that the DRC rules are correctly implemented. 
DRC ERROR COUNT = 45
<img width="1309" alt="25" src="https://github.com/user-attachments/assets/da0ca606-cccc-4951-bc34-157f46e14ed9">



- DRC Next Error for n well used in FEOL   
- Make the following changes in the changes in the DRC tests.

<img width="1396" alt="28" src="https://github.com/user-attachments/assets/50217fac-a445-47f4-8dc1-f102e61ea5bc">

####  Drc Errors solved 
<img width="1309" alt="26" src="https://github.com/user-attachments/assets/52dfc10c-884a-4d7c-9ebf-81a3cfd780fb">
<img width="1396" alt="27" src="https://github.com/user-attachments/assets/486ffa1b-de5e-400d-b6ee-f9d6200314c3">

## Day 4: Timing Analysis and Clock Tree Synthesis (CTS)

### LEF EXTRACTION : 
Tracks and Trunks: The horizontal and vertical lines or paths on which a metal layer is drawn for routing. The width and Height of the standard cell are defined in lambda units of Horizontal track pitch and Vertical trunk pitch.  
Layout Designer converts the layout to LEF format to avoid the disclosure of logic inside the standard cell and make it easy for the physical design engineers.
It includes design rules (tech LEF) and abstract information about the cells.
- Adjusting Grid (Lamda): 
``` bash 
grid 0.46um 0.34um 0.23um 0.17um
```
<img width="1424" alt="30" src="https://github.com/user-attachments/assets/cb0fd41f-0c66-42dc-a11f-5813adc8450d">

Port Creation: 
- Select Ports for LEF :

``` bash 
Port A:  port class input
                port use signal 
Port Y: port class output
               port use signal
VPWR : port class inout
                port use power
VGND: port class inout
               port use ground
``` 
Generate LEF:
``` bash
lef write 
```
This generates sky130_vsdinv.lef file

Make sure you have all the required files (.lib, .lef, .v files) like below

![imp_run 1 ](https://github.com/user-attachments/assets/f7156fb6-fde6-4bb3-9689-f9bf61edc654)


Lef file: 

![im-Run_2](https://github.com/user-attachments/assets/92d4506d-62af-41a9-9abf-9601514d1706)


### Steps to include custom cell to the existing design
Modify the existing tcl file to 

<img width="1403" alt="modified tcl" src="https://github.com/user-attachments/assets/e5ca39f7-82e3-4d84-9aab-0b710afc20d8">

Use the following commands in the openlane interactive mode 
``` bash 
prep -design picorv32a -tag RUN_number -overwrite 
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs
run_synthesis
```
#### After Running the synthesis

<img width="1184" alt="imp_run 3" src="https://github.com/user-attachments/assets/8bb2bed5-6162-4d56-8985-986edb5d43ff">


#### STA log 
<img width="1071" alt="STA log" src="https://github.com/user-attachments/assets/94dfd938-ca26-482e-bd35-9f111d3e5703">

Run_floorplan: If an error occurs use step by step approach instead of run_floorplan
``` bash 
% init_floorplan
% place_io
% tap_decap_or
```


### Delay Tables Key concepts 

![power aware CTS ](https://github.com/user-attachments/assets/744d064e-558f-40cd-8659-202fc904c6ac)

- In CTS, H tree Algorithm Repeaters/buffers are used to make sure that the skew is minimized.
- Delay of cells mostly depends on input transition and output load. 
- During CTS, different input slew and output loads are organized in a delay table with different buffer sizes. 
- The tool uses some equations and ensures the same load at each node by replacing different buffers with different input slew

Placement after sky130_vsdinv
``` bash 
% run_placement
% magic -T sky130A.tech lef read ../../tmp/merged.nom.lef def read results/placement/picorv32a.def &
```
<img width="1320" alt="After placement" src="https://github.com/user-attachments/assets/4718520a-9e27-474b-8662-487ee8fec417">
<img width="1320" alt="Screenshot 2024-07-26 at 4 03 00 PM" src="https://github.com/user-attachments/assets/64bd5049-43f0-46a3-ac3c-5506fc4d4188">


The above placement consists of 145 sky130_vsdinv cells.

### Static Timing Analysis Key Concepts: 
-	Setup Time: Minimum amount of time required for the data to be stable before the active edge of the clock to get properly captured. 
- Setup slack: Data Required Time - Data Arrival Time
- Setup Analysis with Idea Clock:
  ![Picture39 ](https://github.com/user-attachments/assets/b831b368-7d3f-43e2-a704-524e942ed7dd)
![Picture 40 png](https://github.com/user-attachments/assets/5568226c-dfe2-4173-8071-99c87fb8c066)


- Library Setup time (S): Time it takes for MUX 1 to pass to the center of the capture flop.
- Setup Uncertainty (SU): This occurs due to Clock Jitter (Clock may arrive a bit late/early)
  
- Hold time: Minimum amount of time required for the data to be stable after the active edge of the clock to get properly captured. It takes time for the capture flop to send data from the center of the flip flop through MUX 2 to the next cell/flop.
- Hold Slack: Data Arrival  Time - Data Required Time
-  Hold Timing Analysis with Ideal Clocks:
![Picture41 ](https://github.com/user-attachments/assets/89a50334-256b-4833-8a48-ccc8dfed65d7)



### Lab exercise: OpenSTA software 
``` bash 
sta pre_sta.conf 
``` 
- For this, pre_sta.conf is required to carry out the STA analysis. 
- Invoke OpenSTA outside the openLANE flow as follows,
- Initial Slack Violation:

  <img width="1368" alt="Initial Slack Violation" src="https://github.com/user-attachments/assets/834acaed-afd3-457c-ad9a-7098d16af180">
  
Slack Violation after many attempts of changing  cells and strategies :
  
- Attempt 1: SLACK MET

  <img width="1360" alt="Slack Met" src="https://github.com/user-attachments/assets/e2d311db-6fb6-410f-a0cb-483994e515fc">

- Attempt 2: Slack MET
  <img width="1360" alt="SLack Met 2" src="https://github.com/user-attachments/assets/0031b41d-7891-4841-b644-6bf1711d5d63">

### Clock Tree Synthesis:
- Setup Timing Analysis with Real Clocks (Buffers are added and Clock skew is introduced):
  ![Picture44 png](https://github.com/user-attachments/assets/2454fb2a-d223-4644-8fc5-abe05c7362fb)

- 	Hold Time Analysis with Real Clocks (Buffers are added and Clock skew is introduced):

<img width="1084" alt="HT RC" src="https://github.com/user-attachments/assets/8e8248d0-adf4-4f42-86a8-e8c7ff2b201e">

Clock Tree Synthesis Key Concepts: 

-	CTS is an important step that ensures that all the cells reach the clock at almost the same time.
-	It has some quality checks like slew, pulse width, latency, duty cycle, signal integrity, and so on. Using algorithms like H-tree which is particularly effective for distributing clock signals across large chip areas. The hierarchical structure can help reduce clock skew and optimize power consumption.
- Crosstalk: Crosstalk is one of the biggest problems especially at lower nodes due to the high integration density of components on a chip. Since the clock is an important part of the whole circuitry it is important to shield it and protect it from glitches and hazards.

### Lab exercise 
CTS RUN:
``` bash 
run_cts 
```
<img width="1365" alt="run_cts" src="https://github.com/user-attachments/assets/e51ab862-9afe-4317-834f-8fed813f0e5f">

- Use Openroad software and the following commands to meet the timing constraints
``` bash 
openroad
read_lef <path of merge.nom.lef>
read_def <path of def>
write_db pico_cts.db
read_db pico_cts.db
read_verilog /home/OpenLane/designs/picorv32a/runs/RUN_1/results/synthesis/picorv32a.v
read_liberty $::env(LIB_SYNTH_COMPLETE)
read_sdc /home/OpenLane/designs/picorv32a/src/base.sdc
set_propagated_clock (all_clocks)
report_checks -path_delay min_max -format full_clock_expanded -digits 4
``` 
Hold Slack MET:
<img width="1379" alt="Hold slack met" src="https://github.com/user-attachments/assets/f6b50d14-758f-424a-83ff-66236a38f020">


Setup Slack Met: 
<img width="1379" alt="Setup slack met" src="https://github.com/user-attachments/assets/e4036a96-0418-44f7-bc34-473f5c2213a6">


## Day 5: Final steps for RTL 2 GDSII using TritonRoute
### Routing Key Concepts: 
- Routing follows various algorithms and one among them is the Lee Maze Algorithm that starts propagating like a wave and reaches the target and then back tracing is performed where a metal layer is routed from one point to another and then it acts like a blockage to the next routing step.
- 	The Triton Route uses MILP (Mixed Integer Linear Programming: Panel Routing Concept) and consists of two distinct routes, Fast Route – only between two layers and Detailed Route – intra layers.

- Power Distribution Network 
Run the following command 
``` bash
gen_pdn
``` 
### Routing 
For routing, these are the following files 
Inputs: LEF, DEF, Preprocessed route guides
Output: Detailed routing solution with optimized wire length and via count ( observed in MAGIC layout) 
``` bash
run_routing 
 ```
<img width="1379" alt="Run_routing" src="https://github.com/user-attachments/assets/7c8a0c20-e119-4f86-857b-efc838ec4539">

The Wire length is 1712160980 

``` bash 
magic -T  sky130A.tech lef read ../../tmp/merged.nom.lef def read results/routing/picorv32a.def &
```
Layout After Routing 


<img width="1407" alt="magic routing" src="https://github.com/user-attachments/assets/3283ba97-f78a-4482-b931-808fb331a9a5">


Summary of Entire Flow: 

![Picture1](https://github.com/user-attachments/assets/14368544-4514-4723-b7e3-0cbd72eba58d)


 
##  Acknowledgements

I would like to thank Mr. Kunal Ghosh for sharing this workshop and I would like to extend thanks to the GitHub OpenLane community.

## References 
 - [https://github.com/The-OpenROAD-Project/OpenLane](https://github.com/The-OpenROAD-Project/OpenLane)
 - [https://github.com/nickson-jose/vsdstdcelldesign](https://github.com/nickson-jose/vsdstdcelldesign)
 - [https://github.com/AnupriyaKrishnamoorthy/NASSCOM-PD-ANU/tree/main](https://github.com/AnupriyaKrishnamoorthy/NASSCOM-PD-ANU/tree/main)

## Certificate of Completion

![14_VSD nasscom Workshop Certificate 2024GitHub Repo](https://github.com/user-attachments/assets/e96338ce-660d-4374-bcff-224bfe3e9fad)































