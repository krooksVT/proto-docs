---
title: Maintenance
parent: CNC
layout: default
nav_order: 9
---
## Maintenance

### Lubricate Linear Rails
- Check the oil level on the back of the spindle head, top off as needed
- On Laguna control console, press the "Lubricant Pump" button
- Release the button after a few seconds
- After a few seconds, use the remote to manually jog the spindle through the full X,Y,Z range of motion to distribute the oil
- Repeat this operation for every 15 hours of use or after the machine has been sitting idle for an extended period of time

### Spoil Board Leveling
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
- After cut finishes, set 'Z-Spoil' to the stock thickness specified in Fusion
	- VERY IMPORTANT, DO NOT SKIP
