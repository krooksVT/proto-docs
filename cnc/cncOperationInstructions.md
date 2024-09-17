---
title: CNC Operation
parent: CNC
layout: default
---
## Hardware
### Body
![](../attachments/pasted-image-20240425125434.png)
### Tool Head
![](../attachments/pasted-image-20240425152111.png)
### Tool Holder Assembly
![](../attachments/pasted-image-20240425152207.png)
### Tool Rack
![](../attachments/pxl_20240319_145331118.jpg)
- Tool rack has space for up to 8 tools, numbered 1-8 (left to right as viewed from front)
### Pressure Regulator
![](../attachments/pasted-image-20240425144427.png)
### Air Inlet
![](../attachments/pasted-image-20240425143741.png)
### Control Box
![](../attachments/pasted-image-20240425164514.png)
- Vacuum Table - Vacuum Pump 1
- Position Rods - Helps with stock alignment
- E-Stops - Main panel & remote
- Remote - Used to manually position tool head during setup, also has an E-stop
- Lubricant pump - keeps rails lubricated, activate a few seconds, then manually jog toolhead with remote
## File Formats
- Preferred file formats include: F3D, F3Z, STEP, SVG, DXF, PRT 
	- Always verify model dimensions, imported vector files files are not always the correct scale
	- "Inspect" tool is useful for verifying a known dimension
- Can accept OBJ and STL files if necessary
- Do NOT accept generated gcode (.PRG, .NC) files, tool paths must be validated before cutting
## Basic Operations
- Autodesk Fusion 360 is used for the computer aided manufacturing (CAM) operations
- The CAM work consists of the following stages:
	- Create Setup - defines the stock/material that the model will be created from
	- Generate Tool Paths - dictates how/where a specific bit/mill moves, multiple tool paths are routinely used to manufacture parts.
	- Simulation - provides a virtual "mock up" of the tool paths without running the CNC
	- Post Processing - converts tool paths to CNC specific gcode
	- Run - gcode is executing on the physical CNC

![](../attachments/pasted-image-20240423163839.png)
##### Creating Setup
- Switch from "Design" to "Manufacturing" workspace in Fusion (top left corner)
- Setup > New Setup
- On "Setup" tab, set "Stock Point" to the BOTTOM corner of the stock
	- Previous instructions used top of stock & update bit length, this approach is not compatible with multi bit operations
- Still in "Setup" tab, select the model body/bodies to be machined
![](../attachments/pasted-image-20240313132650.png)
- In the "Stock" tab", select "Fixed Size Box" (preferred) or "Relative Size Box" (advanced)
- Set stock dimensions to actual measured values
	- Use calipers to measure stock thicknesses, inaccurate values can result in broken bits
	- X,Y dimensions are important but not as critical as material thickness (Z).
- If the model orientation doesn't fit within the specified stock, use a "Manufacturing Model" to rotate or re-arrange parts to fit.
	- https://help.autodesk.com/view/fusion360/ENU/?guid=MFG-MANUFACTURING-MODEL-OVERVIEW
	- Manufacturing models are useful for [[#Flat Packing a Design|flat packing a design) without modifying the model
		- ![](../attachments/pasted-image-20240424141613.png)
	- Can also be used to cut multiple identical parts from stock by creating a pattern
		- ![](../attachments/pasted-image-20240426144814.png)
		- https://help.autodesk.com/view/fusion360/ENU/?guid=SKT-CREATE-RECTANGULAR-PATTERN
##### Generate Tool Paths
- Tool paths should be generated immediately prior to cutting to ensure bit numbers have not changed.
	- If a tool path takes a long time to generate and/or must be generated in advance, make sure to verify each tool number in the operation vs the tools installed in the machine before starting a cut.
- Utilize template & bit library via Fusion 360 teams (requires invite to proto team) as much as possible
	- https://help.autodesk.com/view/fusion360/ENU/?guid=MFG-REF-TOOLPATH-TEMPLATE-LIBRARY
- Check template library for preset operations, for example 2D contour cut in plywood.
	- Make sure to select "Cloud" templates for most up to date feeds & speeds
	- These templates represent previous successful operations and should be utilized whenever possible.
	- Some operations have several bit size options, in general select the largest bit that meets detail requirements of the design.
	- Settings can be adjusted to meet project specific needs, if they differ significantly from the template settings, consider creating a new template.
	- Make sure to clear any "Selected geometries" in templates (if applicable) and update with geometries from current document
	- Templates use the following naming convention
		- Machine Name, Material, Operation Type, Bit Size
- Note, tool numbers in templates are NOT automatically updated and need to be verified against latest installed tools

####### Toolpaths from Template
- Setup > Create From Template > Select Template
![](../attachments/pasted-image-20240321100257.png)
- Make sure "Cloud" templates are selected unless explicitly using a local template
	- Cloud libraries must be enabled (https://www.autodesk.com/support/technical/article/caas/sfdcarticles/sfdcarticles/How-to-install-a-cloud-tool-library-in-Fusion-360.html)

![](../attachments/pasted-image-20240325143617.png)
- When selecting a contour cut, pay attention to which side of the cut line the bit is on (indicated w/ a red arrow). Clicking on the red arrow will change where the bit cuts relative to the indicated contour line.
![](../attachments/pasted-image-20240321100934.png)
##### Toolpaths from Scratch

- 2D Adaptive preferred over 2D Pocket, high speed machining (HSM) limits how much of the bit is cutting at a given time, improving tool life and reducing breakage
	- https://help.autodesk.com/view/fusion360/ENU/?guid=GUIDA73542E9-ED9C-4BD9-A87D-3A0ECA8BEB41
	- Optimal load: 0.25 x tool_diameter
	- Depth of cut: Max 50% diameter of tool
###### 2D Adaptive
- Critical parameters
	- Tool Tab
		- Feed per tooth
			- Indicates how much material is each revolution by each cutting edge when the machine is performing a normal cut, typical values range from (0.002 to 0.01")
		- Plunge Feed per Revolution
			- Same as feed per tooth but specific to vertical cutting/drilling, end mills have different geometry than drill bits and are not as efficient cutting straight down into material. Typical values are ~50% of the feed per tooth value (0.001 to 0.005")
	- ![](../attachments/pasted-image-20240627150629.png)
	- Passes Tab
		- Optimal Load
			- Indicates the maximum amount of tool engagement, as a rule of thumb this value should be approximately 25% of the tool diameter
				- For example, a good starting point for a 3/8" endmill is 0.09375" (0.375 * 0.25)
			- This value can be increased or decrease when machining harder/softer materials (lower for hard materials, higher for soft materials)
		- Maximum Roughing Stepdown
	- ![](../attachments/pasted-image-20240627150658.png)
##### Simulation
- All generated tool paths should be simulated prior to post processing
- Individual or multiple tool paths can be simulated by selecting desired operations (or setup for all), then selecting "Simulate"
![](../attachments/pasted-image-20240325132025.png)
- Use the play/pause buttons at the bottom of the screen to preview the operations.
- Watch for any errors/warnings in simulation timeline (indicated by vertical red lines)
- Do NOT post process any operation that has simulation errors, it could result in damage to equipment and/or stock. 
- Hovering over errors will give a description of issue that must be addressed in the tool path settings.
![](../attachments/pasted-image-20240325131546.png)
##### Post Processing
- Post processing creates the gcode file for a specific CNC
- For setups with multiple operations, can either generate a single file or multiple files
- If a single file is used with multiple bits, the Laguna automatic tool change (ATC) system will swap bits when each operation is complete.
	- As previously noted, for the ATC system to work properly and avoid damage to equipment/stock, setup origin must be located at the bottom of the stock and all bits must be zero'd to the spoilboard using the "Automatic Touch Off" buttom on the Laguna Control Screen.
- Consider using multiple individual files when
	- Individual operations are estimated to take a long time (estimated machining times are shown in the bottom right corner of the screen)
	- Manual tool changes are required
	- Parts need to be inspected after an operation before proceeding
![](../attachments/pasted-image-20240325133105.png)
- The "Post Processing" screen allows the output files to be named (if not already configured via the Setup > Post Processing tab) and specifies an output directory for the gcode file (.PRG)
- Make sure the "Laguna CNC / laguna" post processor is selected, if not use the navigator to find it via Fusion's cloud library.
- The units should be set to "Document unit" to ensure tool paths are scaled correctly.
- Select "Post" to generate the gcode
- Copy the output file to a USB drive for use in the CNC Control System
![](../attachments/pasted-image-20240325134011.png)
## Worked Examples

### Flat Packing a Design

<iframe width="560" height="315" src="https://www.youtube.com/embed/VvK5Ak8Jhws?si=yMYRji9WpdwUAiTc" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

### CAM

<iframe width="560" height="315" src="https://www.youtube.com/embed/KtmaGfm4HOU?si=fjQGdTkC4-evBt47" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

## Procedures
### Startup Procedure
- Turn on air pressure using [[#Air Inlet|wall mounted valve)
- Verify pressure on [[#Pressure Regulator|regulator) is approximately 0.45 MPa
- Turn on power using the [[#Control Box|power switch) on the control box
- Switch key to "ON" (clockwise)
- Press green power button
- Wait for system to boot
### Shutdown Procedure
- Park any installed tooling (Home screen > Park)
- Press green power button
- Switch key to 'OFF' position (counter-clockwise)
- Turn on main power
- Turn off air
### Running Program
- Insert USB device into the control panel
- Copy project file from USB
	- From the control screen
		- Setup > Program Manager
		- Select "USB" tab on left
		- Select desired file (.PRG extension)
			- Can sort by name, date, size
		- With file selected, press "Copy"
		- Select "Programs" tab on left
		- Select "Paste"
- Verify GCode
	- From control screen
		- Setup > Verify G-Code
		- If program not already select use drop down to select
		- Select "Load"
		- Select "Check Code"
		- Wait for check to complete, indicated by a blue check mark or a reported error
			- Checkmark does NOT stay on screen, must watch for it  
![](../attachments/pxl_20240319_172848682.jpg)
- Load a tool into the toolhead (if not already loaded)
	- Option 1 - Auto Touchoff (Recommended)
		- Setup > CNC Tool Data 
		- Select tool number from dropdown
		- Select "Execute Automatic Touch On/Off"
	- Option 2 - MDI Command (Advanced)
		- Setup > CNC Settings
		- MDI Command
			- "M06 T\<tool number\>" e.g.  `M06 T2`  // load tool \#2
- Position the toolhead at the X,Y origin of the stock
	- Use the handheld remote to manually jog the toolhead
	- Press and hold the "Enable Switch" on the remote to allow motion
		- Will automatically open the "JOG" screen
	- Select movement axis using left dial (X,Y,Z,4)
		- Mill does not currently have a 4th axis
	- Select the movement multiplier using the right dial (1x,10x,100x)
		- Avoid using the 100x multiplier when moving the Z axis, it could result in bit breakage
	- With bit position over the X,Y origin of the stock, press "Teach ZPO"
		- The dust boot can be raised on the Laguna control screen to provide a clear view of bit during alignment
	- **DO NOT PRESS** "Teach Tool Length", tools should only be zero'd using the auto touch off system
	- Verify X,Y origin was set correctly
		- With "Zero Point Offset" selected from the drop down (not Machine Coordinates or Relative Coordinates), verify X Axis and Y Axis read <= 0.001 in
![](PXL_20240319_173002852 1.jpg)

- Run through the [[Laguna Pre-Cut Checklist)
- From main screen press "Run"
- Pause Operation (Optional) 
	- To pause operation, press "Hold" from main screen
		- Spindle remains on by default when paused
		- Spindle can be stopped (optional) via
			- Main > Settings
			- Set "Spindle Override" to 0%
![](../attachments/pxl_20240319_173301916.jpg)
- Resume Operation (Optional)
	- If spindle was turned off, (0%), set "Spindle Override" to 100%
		- DO NOT resume cut operation with spindle at 0%, this could damage bits or the machine
	- From "Main" tab, press "Run" 
- Emergency Stop (Optional, hopefully)
	- The emergency stop buttons on the Control Box or Remote can be pressed at any time to immediately stop all machining. 
	- Program stopped via E-Stop cannot be resumed (i.e. E-Stop is not a pause)

## Advanced Operation

#### Work Holding
 - Proper work holding is critically important, particularly for small pieces. It is very easy for a piece to come lose and come into contact with the mill bit, causing damage to the work piece and potentially damage the bit.
 - There are many options for work holding including available and should be utilized on a per job basis.
	 - Double sided tape
	 - Tabs (configurable in Fusion 360)
		 - https://help.autodesk.com/view/fusion360/ENU/?guid=MFG-REF-2D-CONTOUR-TABS
	 - Clamps / Fixtures
	 - Screws / Staples
	 - Vacuum
- When utilizing work holding that could interfere with the mill bit, caution must be taken to ensure the clamps/fixtures will not collide with the tool head.  
- Soft materials such as foam blocks can be used to mock up actual clamps for verification.
	- We won't actually be cutting material at this point, we will only "cut air".
	- We will run the operations and observe whether any part of the tool head contacts the foam blocks.
	- If the operation completes issue, we will install clamps in the EXACT space where the foam was located.

![](../attachments/pxl_20240320_165610904.jpg)
![](../attachments/pxl_20240320_170425712.jpg)
#### Feeds & Speeds
- Many tool manufacturers provide feed & speed recommendations for their tools. These settings provide a good starting point and should be utilized when dialing in settings for new materials. It is important to note the RPM ratings on bits and not to exceed these values when setting up operations.
- Most of the tools used in the Laguna CNC are from Amana Tools. Amana has a Fusion 360 library of their bit offerings, including all the relevant numbers and measurements which describe the tools geometry (number of cutting flutes, length of flutes, bit diameter, etc).
#### End Mill Anatomy

![](../attachments/pasted-image-20240321105401.png)
- https://www.endmill.com.au/blog/choosing-the-right-end-mill-for-the-job/ // selecting bits, good read, recommended 
- Amana Fusion 360 Tool Library
	- https://www.amanatool.com/view-amana-tool-fusion-360-library
	- [[Amana-Tool-Fusion-Master.tools)  // available via Prototyping Studio team cloud
- Sample bit
	- https://www.amanatool.com/46420-solid-carbide-spiral-plunge-3-8-dia-x-1-1-4-x-3-8-shank-down-cut.html?ff=1&fp=8806
- Sample feeds and speeds
	- [[Solid-Carbide-Spiral-Plunge-2-3-Flute-v26.pdf)
	- https://www.amanatool.com/pub/media/productattachments/Solid-Carbide-Spiral-Plunge-2-3-Flute-v26.pdf
#### Adding / Swapping Bits in Tool Change Rack
![](../attachments/pxl_20240321_172025118.jpg)
- Choose collet corresponding to bit shank diameter
	- Collet size engraved on front face
	- 1/8", 1/4", 3/8", 1/2" imperial sizes available
	- 4mm, 6mm, 8mm metric sizes available
- Tighten bit in collet by hand (clockwise)
	- All cutting flutes should be below collet
	- The bit shank should be inserted a minimum of 2/3 (100% ideal) of the collet length
		- For example, a 1.5" length collet requires at least 1" of bit shank inserted, however ideal insert length is 1.5"
- Finish tightening bit in collet using "ER 32" wrench & CNC tool mount bracket
![](../attachments/pxl_20240319_144259489.jpg)
![](../attachments/pxl_20240319_171946523.jpg)
- Press & hold green button on side of spindle
-  ![](../attachments/pxl_20240319_172014827.jpg)
- Position tool holder under spindle head
- Release green button

<iframe width="560" height="315" src="https://www.youtube.com/embed/HDV3oebI2bc?si=NWdhRKGc_imuj1HE" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

- On the CNC screen, update the "current tool" number to reflect which tool position the new bit will be physically located in
- Park tool 
- Execute "Automatic Touchoff"
- Update Fusion cloud tool library to reflect new tool numbering
#### General Tips
- It's often a good idea to "cut air" prior to cutting the actual material. This is accomplished by intentionally setting the material thickness in your Fusion360 setup to a value 2-3x larger than the actual material thickness. When the operation is run on the CNC, the bit should remain in the air rather than touching the material or spoilboard. By observing the "fake" cut (with the e-stop in hand), you visually verify the toolpaths are what you expect. This method can catch potential tool plunges or unit issue (mm vs inches). 
- The Laguna control panel has a "axis limit" setting which could be helpful in single bit operations. This could be set to the tool offset length, ensuring the gcode can't move the bit below the top of the spoilboard. The "check gcode" tool will throw a warning if the operation attempts to go below this limit.
	- Note - for this to work, the tool must be loaded already as retrieving the bit from the tool holder requires more Z travel.
- Verify all tool lengths are approx. -10 inches, anything significantly less (0-3 inches) could indicate a bit has been previously zero'd on top of stock.
## Maintenance
#### Lubricate Linear Rails
- Check the oil level on the back of the spindle head, top off as needed
- On Laguna control console, press the "Lubricant Pump" button
- Release the button after a few seconds
- After a few seconds, use the remote to manually jog the spindle through the full X,Y,Z range of motion to distribute the oil
- Repeat this operation for every 15 hours of use or after the machine has been sitting idle for an extended period of time
#### Spoil Board Leveling
- Minimum spoil board thickness is 0.5"
- Be mindful of the position rods when leveling
	- Rods should be in the down position when cutting
	- Cutting depth should be at least 1/8" above position rods in stowed position 
- In Fusion 360
	- Create extruded rectangle corresponding to spoil board size
		- Alternatively use pre-made project https://a360.co/4aps348
	- Use 'Flycut Template'
	- Make sure the stock thickness in setup is identical to model thickness
		- Model should not be centered within larger stock (e.g. 0.6" model in 1" stock)
	- Make sure origin is located at bottom of model
- On Laguna Control Screen
	- Setup > CNC Positions
	- Set 'Z-Spoil' to 0.0
- Set X,Y zero point on corner of stock
- Run program, should remove less than 1/8" of material
- [[#Running Program|Run the program)
- After cut finishes, set 'Z-Spoil' to the stock thickness specified in Fusion
	- VERY IMPORTANT, DO NOT SKIP
## References
### Pre-Cut Checklist
[[Laguna Pre-Cut Checklist)
### Project Showcase
[[CNC Project Showcase)
