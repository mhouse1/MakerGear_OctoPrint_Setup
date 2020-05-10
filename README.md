forked from original on 5/10/2020 12:10pm

# OctoPrint-MGSetup
This plugin provides general setup and configuration interfaces for control of the MakerGear M3 Single and Independent Dual Extruder printers.

## Setup

We strongly recommend only using full Release versions of this plugin, as those have been fully tested and release for general use.



To install the absolute latest, currently testing version:

Install via the bundled [Plugin Manager](https://github.com/foosel/OctoPrint/wiki/Plugin:-Plugin-Manager)
or manually using this URL:

    https://github.com/mhouse1/MakerGear_OctoPrint_Setup/archive/master.zip

# Other recommendations for M3 printers
## octoprint update
    as of 5/10/20 M3's are still shipped with 3.1.6; I Recommend at minimum octoprint 3.1.12 to support: octofarm for managing multiple printers, and creating backups
## change hostname
The default hostname will be octopi If you only have 1 system on your network, this will probably be fine. Some users will have multiple systems and this will cause a problem with networking.

To change the hostname.

Log in via SSH (of if you have a screen & keyboard hooked up, use that). The default host is: octopi.local Username: pi Password: raspberry
At the command prompt type sudo nano /etc/hostname
Change the hostname from octopi to your preferred hostname. Do not leave spaces before or after and I would recommend keeping it all lowercase.
Ctrl-X and Y to save.
At the command prompt enter sudo nano /etc/hosts
Find the line with 127.0.1.1 and octopi
Replace octopi with exactly the same hostname as you entered into the hostname file. Note: This has to exactly match, so the same CaSE.
Reboot, using sudo reboot

## Octoprint > Settings Dialog > GCODE scripts
modified to support filament change mid print and other preferences
### before print starts
M300 S110 P100
M300 S130 P100
M300 S146 P100
M300 S164 P100
M300 S174 P500
M300 S220 P100
M300 S196 P500
M300 S233 P100
M300 S220 P500

;always preheat both at the same time before starting print
M140 S70; set bed temperature
;M190 S70; wait for bed temperature
M104 S250 T0; set hot end temperature
;M109 S250 T0; wait for hot end temperature

G211 S1 ; turn on software endstops

### after print job completes
M300 S185 P500
M300 S138 P500
M300 S185 P500
M300 S277 P500
M300 S277 P500
M300 S246 P250
M300 S233 P250
M300 S246 P750

### after print job is canceled
; disable motors
M84

;disable all heaters
{% snippet 'disable_hotends' %}
{% snippet 'disable_bed' %}
;disable fan
M106 S0

G1 Z100 ; Move bed down

### after print job is paused
M300 S92 P250
M300 S138 P250
M300 S185 P250
M300 S233 P250
G91 ; Relative movement
G1 E-6 F1000; Retraction move, take away filament pressure
G1 Z30 ; Move bed away (downward vertically)
G90 ; Absolute position ON
G1 X0 F2500 ; Move to left edge of print bed
G1 Y0 ; Move to front position (two moves to avoid risk of running into bed clamps)
G91; Relative movement
G1 E-80 F1000; retract filament into bowden for easier swap
G90; Absolute position ON

### before print job is resumed
G91 ; Relative movement
G1 E40 F300; Reposition filament (or purge if new filament has been pushed in by hand)

; ========alert music before resume=========
M300 S110 P100
M300 S130 P100
M300 S146 P100
M300 S164 P100
M300 S174 P500
M300 S220 P100
M300 S196 P500
M300 S233 P100
M300 S220 P500

G90 ; Absolute position
G1 Y50 F3000; Move past the front left clamp to be safe
G91 ; Relative movement
G1 Z-30 ; Move bed back (upwards vertically)
G90 ; Absolute position