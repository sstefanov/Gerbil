# Gerbil's Inkscape Plug Ins

These Inkscape plug ins have been modified to meet the G-code standard so the can run smoothly in Gerbil's (Grbl) firmware. The engraving Plugin has been modified extensively while the Jtech photonics vector plugin has been only modified slightly (remove the M18 command). You can use these Inkscape plug ins to generate gcode for the basic Grbl Arduino Uno set but the engraving plugin has been modified especially for the customer Gerbil controller to match the Chinese K40 (Stock CO2 laser tube and laser power supply).

First step: Download the Inkscape plugins from above via the button "download as zip". Drag the files .inx and .py into your Inkscape/Share/Extensions folder (MAC or Windows). The other files are example of g-code files and a streamer script (for huge files).

Second step: Start Inskcape and see if the Plugins turn up under the extensions menu.

- Laser commands being used by Gerbil are:
- Laser On: M4 (M3 can be used too but only for cutting)
- Laser Off: M5

Engraving settings for 256 Gray levels:
- Max resolution 382 DPI or 15Pixels/mm with a max speed of 1000mm/min
- Lower resolutions can use higher engraving speed (up to 2000mm/min)

Once the laser head starts sputtering then you know you are driving your controller too hard and you should reduce its speed.

## Set Inkscape import resolution:
The import a picture from the File function from the Import menu uses a default import resolution of 96 dpi. So that reduces your pictures to 96 dpi. To change that:
- Open the preferences in the edit menu (Shift + Control + p).
- Select Bitmaps and change the default import resolution to 381 dpi

## Using the Raster Engraving plugin:
- Import a picture from the File, Import menu
- Scale (resize) the canvas in Inkscape to the dimensions of the engraving area you need via "Document properties" ("resize page to content" feature allows you to do this automatically). So, basically your A4 resizes to the engraving size.
- Select the K40 Engraving Plugin from the Extensions menu
- Select the engraving mode "Black and White" or "Gray scale", the levels of Gray (max 256), speed (max 1000 for highest resolution)
- Set the directory (for G-code output files)
- Set the file name
- Hit the generate button (which produces the G-code file into the directory you have configured)

## Using the Vector Cutting Plug in:
The instructions for using of JTech Inkscape plug in can be found here: https://jtechphotonics.com/?page_id=2012
In addition you have to install svgpathtools from here: https://pypi.org/project/svgpathtools/

Also this plugin allows engraving via vectors (not rastering) and describes how to combine vector engraving and cutting. The download version on here on Github has been altered: the M18 command has been removed to make it compatible with Grbl.
In inkscape, mirror the Y axis via the button "Flip selected objects horizontally) so that everything looks mirrored in Inkscape and in CNCJS (when uploaded). This is required so the user of the K40 does not have to change their Y axis end stop.
Letter engraving or cutting.
Select the letter tool from inkscape (Big "A" character icon) tools icons on the left vertical bar, write some text on the canvas (Document properties set to "A4, landscape"). Select from top ribbon menu, option "Text", "put on path".  Select from top ribbon menu "Path", "Object to path", select from menu "Extensions", the "Generate G-code/J-Tech Photonics" plugin, generate gcode. Now you can vector engrave or cut letters and numbers (from acrylic for your house and council garbage collection bins!).

## Sending it to the Gerbil Controller
There are two options for sending the generated gcode files to Gerbil: via CNCjs and via streamer.py script.
Note: Next to this, I have added the gcode streaming feature within each Plugin. This feature is undergoing extensive testing first so beware it is experimental at this stage.
- Install a G-code sender
- For example https://github.com/cncjs/cncjs or for simple fast install as desktop app (MAC or Windows) https://github.com/cncjs/cncjs/wiki/Desktop-App
- Select Grbl as format
- Select the com port (refresh button at the end of the list dropdown shos the com ports)
- Hit the "Connect" button
- See whether it responds and click on the clear alarms "Unlock" button (on the top/right of the screen)
- See if you can jog the machine via the arrows (set the appropriate step size via "custom" into 10 steps per jog command)
- The jog speed or feed needs to be adjusted for the K40. You do this via the cog wheel next to the "Axis" screen section (on the left/middle). Click on the cog and a menu opens where you can set the max speed to something like 1000 or 1500.
- Upload a raster or vector file (max 10Mb, although can be changed in code)
- Hit the "Play" button in the top bar
- The gcode is now send to Gerbil and you can see the progress on the screen in form of completion and visual movement in the gcode depiction
- The visual reporting bars for the processing queues move. You can change on the fly the F-feed rate, S-Strength of the laser beam, R-receive buffersize.

Caveat for cncjs: files can be not large unless you change the source code to allow bigger files (limit is configured to 10Mb because the did not envisage the use of this for laser engraving). You can change this in the source code but installation from scratch with maven and node js is a hell. I asked to increase the size which they did for one release and it was back to 10Mb in the next release, sigh.

## Streamer.py for streaming large gcode engraving files to Gerbil

Potentially the python script will run because you installed Inkscape which is build on Python. I had mixed experiences with this (one laptop did while the other didn't).
You might need to have Python installed on your machine to use it via the command line.
Use https://www.python.org/downloads/ to download and install Python separately, if you run into issues where the computer MAC or Windows cannot execute the py script from the command line tool.
Inkscape itself comes with a Python install inside the Inkscape folder. Using the environment path settings in Windows, you should be able to used that as well.
Install Inkscape 0.92.0 32 bits not 0.92.2 (buggy) or 64 bits(does work well will some Python liberaries like PySerial)


Cmd Streamer.py <name_of_the_file> <connectedcomport>
For example:
>c:streamer.py name_0001_Gray_256_gcode.txt com1

The comm port can be looked up quickly via CNCjs if you don't know it.

Alternatively you can enter streamer.py which shows you the option keys

If you stil run into python errors than you might need to install another Python library.
Use the command line tool (MAC or WIN) and type:
>python setup.py install

Now everything should work smoothly!

## Sorting paths for better laser cutting

When cutting with laser or CNC it is very important to cut first most inner paths to keep whole part in his place.
Unfortunatelly, there was no such sorting in the Inkscape plugins till now and I decide to implement them here.
This is modification of the plugin which sort paths and make g-code better for use in cutting machine.

Paths are sorted following way:
First all paths are divided by subpaths and sorting is applied on subpaths. This is important if there are combined paths.
Next is generated tree of paths according which path contain other.
Then tree is sorted from the top using minimal distance. When top path is found sorting is repeated recursively for all subpaths inside following same subtree rules.

As result g-code become more optimal and convinient for cutting parts.

To use this plugin please install svgpathtools from here:
https://pypi.org/project/svgpathtools/
