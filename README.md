# Noze Wipe
A Voron nozzle wiper using silicone parts meant for the [Blackbox 3D Printer](https://layershift.xyz/blackbox3dprinter/). You can find these parts on [kb-3d](https://kb-3d.com/).

![[FinalResult.png]]

The point of this mod is to provide a non-abrasive specifically designed nozzle wipe that is compatible with [Voron-Tap](https://github.com/VoronDesign/Voron-Tap), [Nevermore](https://github.com/nevermore3d/Nevermore_Micro), and the [Kinematic Bed](https://github.com/tanaes/whopping_Voron_mods/tree/main/kinematic_bed) mods.
## BOM
|Part|Amount|
|-|-|
|M3x8 SH|4|
|M3 T-nut|2|
|M3 Heatset-insert|2|
|6x3mm magnets|4|
|[Silicone Leak Blocker](https://kb-3d.com/store/cnc-laser-cut/22-silicone-leak-blocker-blackbox-1634506066001.html#c112/fullscreen/q=silicone)|1|
|[Silicone Nozzle Wiper](https://kb-3d.com/store/cnc-laser-cut/9-silicone-nozzle-wiper-blackbox-1634505999959.html)|2|
## Instantiation
### Physical
1. Print the parts using the [Voron printing settings](https://docs.vorondesign.com/sourcing.html#print-settings).
3. Carefully slip the [Silicone Nozzle Wiper](https://kb-3d.com/store/cnc-laser-cut/9-silicone-nozzle-wiper-blackbox-1634505999959.html) into the vertical slots on the holder.
4. Install heat-set inserts into the side of the holder.
5. Use hot glue or similar adhesive to affix the [Silicone Leak Blocker](https://kb-3d.com/store/cnc-laser-cut/22-silicone-leak-blocker-blackbox-1634506066001.html#c112/fullscreen/q=silicone).
6. If you want the bin, use hot glue or similar adhesive to affix magnets into the remaining holes.
7. Install t-nuts with holes closest together on the left outside bed rail.
8. Use 2 M3x8 SHs to install the mount.
9. Lightly screw the remaining M3x8 SHs into the holder and tighten when placed at the correct height on the mount. The top of the holder and bin should be level with the bed.
10. Done!
### Klipper

This is where it gets more complicated. There are several steps you will have to do.

1. Find the physical limits of your printer.
	- Allow Klipper to go past the extent of the bed.
2. Calibrate the locations of the wipe.
3. Testing.
4. Modify print_start.

#### Physical limits
To use this mod, the printer must travel past the normal limits of the bed. To do this, adjust the position_max and position_min in [stepper_x] and [stepper_y] sections of the printer.cfg.
You may also have to adjust the bed so that the nozzle can go past one side of the bed.

My config looks like this.

```ini
[stepper_x]
...
position_min: 0
position_max: 250
...

[stepper_x]
...
position_min: -7
position_max: 250
...
```
#### Installation
Copy and paste the NozeWipe.cfg file to your kilppers /config directory. Next, use [include NozeWipe.cfg] at the beginning of the printer.cfg file. Three locations must be defined; otherwise, you will get an error.

#### Calibration
```ASCII
                     ▐
                     ▐        BED
                     ▐
                     ▐
      y = 0╶╶╶╶╶╶╶╶╶╶▐▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄
                     ╎    ╭──────────╮╭─┼─┼─╔═══╗╮
                     ╎    │╭────────╮││ ║ ║ ║   ║│
      all_y╶╶╶╶╶╶╶╶╶╶┼╶╶╶╶╶│╶╶╶╶╶╶╶╶│╶╶╶║╶║╶║╶╶╶║╶╶╶╶╶╶╶╶╶
                     ╎    ││ BIN    │││ ║ ║ ║   ║│<-HOLDER
                     x    ││      ∧ │││ ║ ║∧║ ∧ ║│
                     =    │╰──────┼─╯││ ║ ║╎╚═╎═╝│
                     0    ╰───────┼──╯╰────┼──┼──╯
     wipe_x╶╶╶╶╶╶╶╶╶╶╶╶╶╶╶╶╶╶╶╶╶╶╶┘        ╎  ╎
 wipe_end_x ╶╶╶╶╶╶╶╶╶╶╶╶╶╶╶╶╶╶╶╶╶╶╶╶╶╶╶╶╶╶╶┘  ╎
block_loc_x╶╶╶╶╶╶╶╶╶╶╶╶╶╶╶╶╶╶╶╶╶╶╶╶╶╶╶╶╶╶╶╶╶╶╶┘
```
The ASCII diagram above points to the locations that the nozzle will travel to. You must fill out six variables in the NozeWipe.cfg [gcode_macro POS] section. These are indicated by being set to 10000. Setting the values to 10000 allows the printer to start and move but not error out.

```INI
[gcode_macro POS]
variable_z_min_height: 10000
variable_z_lift_height: 10000
variable_all_y: 10000
variable_wipe_x: 10000
variable_wipe_end_x: 10000
variable_block_loc_x: 10000
...
```

##### Calibration Steps 
1. Home all.
2. Quad-Gantry-Level (QGL).
3. Go to (0,0,5).
4. Move y by 1mm to be past the bed and above the bin and holder when you find the correct location. Record the y value in the all_y variable.
5. Move x to be over the bin and record that value in wipe_x.
6. Move x between the wipes and the block and record the value in wipe_end_x.
7. Move x to be over the block and record the value as block_loc_x.
8. Move z down to be slightly pressing into the block. Record the value as z_min_height.
9. z_lift height should be around 10mm (in absolute coordinates). This value should clear all parts of the wiper.
10. Save and restart!

My resulting config looks like this:
```INI
[gcode_macro POS]
variable_z_min_height: 1.8
variable_z_lift_height: 5
variable_all_y: -7
variable_wipe_x: 0
variable_wipe_end_x: 17
variable_block_loc_x: 25
```
#### Testing
1. Ensure the printer does not crash while traveling to the new limits.
2. Home and QGL.
3. Use command \_Wipe_Home and check location.
4. Use command \_Wipe. The nozzle should wipe between the start and right before the block five times and lift after.
5. Use command \_Park_nozzle and check location. Adjust any of these values as needed using the ASCII diagram.
6. Save and restart!
#### Modify print_start

You will need a sequence that looks something like this:
1. Home.
2. Quad-Gantry-Level (QGL) or equivalent sequence.
3. ex. Heat bed and nozzle.
4. Use \_Wipe_Home (must be done before \_wipe)
5. Use \_wipe.
6. ex. park and cool to bed, safe temp.
7. Home with a clean nozzle.
8. ex. \_park_nozzle and heat.
9. Print!.

Note: The ex. sections are where you may want to do things differently than I did.

The following is what my print_start macro looks like.
```INI
[gcode_macro PRINT_START]
gcode:
G90 ; absolute positioning
CLEAN_NOZZLE EXTRUDER={params.EXTRUDER|float} BED={params.BED|float}
STATUS_HOMING
G28
{% set FILAMENT_TYPE = params.FILAMENT|default(PLA)|string %}
{% if FILAMENT_TYPE == "ABS" or FILAMENT_TYPE == "ASA" or FILAMENT_TYPE == "PET" %}
SET_FAN_SPEED FAN=Nevermore SPEED=0.8
{% endif %}
STATUS_HEATING
_PARK_NOZZLE
M104 T0 S{params.EXTRUDER|float}
M190 S{params.BED|float} ; Make sure bed is hot
M109 T0 S{params.EXTRUDER|float} ; Make sure hotend is hot
_WIPE_HOME
G92 E0
G1 E{20} ; Purge
G92 E0
_WIPE WIPE_COUNT=1
STATUS_PRINTING
```
