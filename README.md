
<h1 align="center">
  <br>
  <img src="./Photos/Logo/KAMP-Logo.png" width="260"></a>
  <br>
    Klipper Adaptive Meshing & Purging
  <br>
</h1>

<h4 align="center"> Your 3D printer just got a whole lot smarter!</h4>
    <br>
<p align="center">
<img src="./Photos/Badges/built-for-klipper-made-with-love.svg" width="400">
</p>

<p align="center">
  <a href="#key-features">Key Features</a> •
  <a href="#requirements">Requirements</a> •
  <a href="#installation">Installation</a> •
  <a href="#adaptive-purging">Adaptive Purging</a> •
  <a href="#troubleshooting">Troubleshooting</a> •
  <a href="#credits">Credits</a>
</p>

<p align="center">
<img src="./Photos/Meshing-Assets/adaptive_benchy_glow.gif" width=300>

## Key Features:

* No wasted probe information
  - KAMP will generate a mesh **only** in the area you actually need it: Where you're printing!
  - Since the mesh area will be smaller, the mesh can be much more dense. Imagine making a 3x3 mesh, but the size of a [3DBenchy](https://www.3dbenchy.com)!

* Compatibility
  - If you've got a 3D printer running Klipper and a probe, KAMP is ready to serve you.
  - We've seen users have success with inductive probes, [Klicky](https://github.com/jlas1/Klicky-Probe), [Euclid](https://github.com/nionio6915/Euclid_Probe), BLTouch, and [Voron Tap](https://github.com/VoronDesign/Voron-Tap)
  
    >**Note:**
    >KAMP  has the option to fuzz mesh points, which helps to spread out wear from nozzle-based probing.
* Straightforward setup
  - One of the main goals of KAMP was to be as easy to implement as possible. Just `[include]` a few files, and you're ready to go!


* No parameter passing
  - KAMP uses information from [[exclude_object]](https://www.klipper3d.org/Exclude_Object.html#exclude-objects) to define mesh bounds. Complicated slicer parameter passing is finally a thing of the past. Welcome to the future!

* [Adaptive Purging](#adaptive-purging) 
  - Allows you to purge right next to the print area. We don't believe in boring purges, we like to sign our work with purge logos! We also have provisions for [more simplistic]() purges as well.

<p align="center">
<img src="./Photos/Purging-Assets/voron-purge-example.png" width="375" >
</p>

<p align="center">
    <b>
        Adaptive Voron Logo Purge
    </b>
</p>

## Requirements:

1. You will need `[exclude_object]` defined in `printer.cfg`.

2. You will also need to make sure the following is defined in `moonraker.conf`:
  
    ```yaml
    [file_manager]
    enable_object_processing: True

    ```
3. Finally, you will need to make sure your slicer is labeling objects:

<p align="center">
<img src="./Photos/Meshing-Assets/slicer-setting.png" width="500">
</p>

<p align="center">
If you've got all that, you're ready for KAMP!
</p>

## Installation:

The cleanest and easiest way to get started with KAMP is to use Moonraker's Update Manager utility. This will allow you to easily install and helps to provide future updates when more features are rolled out!

1. See Release



## How to use `KAMP_Settings.cfg`:
<br>

  >**Note:**
  For ease of use and understanding, all KAMP configuration is contained inside of `KAMP_Settings.cfg`. Any changes you wish to make to KAMP specifically can be found here.

<br>

## Adaptive Purging:

* These settings directly affect how KAMP handles it's purge routines and placement:

  * `purge_height:` This is the height above the bed that the nozzle will be when KAMP does it's purge. This should not need much adjustment, unless you are using a nozzle with a large diameter, or purging a very small amount. The default for this value is `0.8`.
  
  * `tip_distance:` This is the distance that the very tip of your loaded filament is away from the opening of your nozzle. It's a good idea to tune this value so your purge is nice and consistent, rather than spotty or blown out at the beginning. It's a good idea to set this value to be a little bit less than the final retract that happens in your `Print_End` macro.

<p align="center">
<img src="./Photos/Purging-Assets/tip-distance.png" height="350" >
</p>

  * `purge_margin:` This is the amount of space you wish to have between your purge, and your actual print area. Helpful for those who print using brims or skirts, or have a printhead with ducts that are very close to the bed during the first few layers of a print. By default, this value is `10` as a healthy, but conservative buffer.

  * `purge_amount:` This is the amount in millimeters of filament material you wish to purge prior to a print beginning. Some users prefer only to purge enough to get the nozzle ready, and some users prefer to purge enough to change a filament color before a print. After some testing, the default amount to purge is `30` millimeters of material.

  * `flow_rate:` This is the desired flow rate you wish to purge at. You should set this value to be close to the flow limit of your hotend. Standard flow hotends like MicroSwiss, Dragonfly, or Revo should be around `12` or so, while higher flow hotends, like the Rapido or Dragon, should be set somewhere around `20`.

  >**Note:**
It is required to add `max_extrude_cross_section: 5` to your `[extruder]` config to allow effective purging to be possible. KAMP will warn you if you forget to set this value, and skip the purge so the printer will not be halted. It is also recommended to set up [firmware_retraction](https://www.klipper3d.org/Config_Reference.html?h=retract#firmware_retraction) inside of klipper so KAMP can use the correct retraction settings for your machine. If this is not found, KAMP will warn you, and use reasonable direct-drive extruder values to complete the purge.

## Smart Park:

* These settings are used for adjusting KAMP's Smart Park function, which is helpful to move the printhead near the print area to do your final heating.

  * `smart_park_height:` This is the height at which you'd like your printhead to be when calling the `Smart_Park` macro. **Don't forget to add `Smart_Park` near the end of your `Print_Start` macro, *before* the final heating command is called.**

## Troubleshooting:

<details>
    <summary>
        <b>
        No matter what, meshes and purges are not adapting!
        </b>
    </summary>
<p>
</p>
This happens when Moonraker's object processor is putting exclude object definitions in the wrong spot (AFTER Print_Start rather than before). This is usually fixed if you add M117 before your print_start macro in your slicer's starting gcode. M117 is just a basic "clear display" gcode, but will force the preprocessor to place the definition macros in the correct spot.
</details>

<details>
    <summary>
        <b>
        I'm getting 'gcode_macro BED_MESH_CALIBRATE:gcode': TypeError: bad operand type for abs(): 'Undefined'
        </b>
    </summary>
<p>
</p>
This was likely caused by you commenting out a setting in KAMP_Settings.cfg rather than just setting it to false. These checks needs to be in place, so instead of commenting them out, just set them to disabled.
</details>

<details>
  <summary>
    <b>
      Purging is not working!
    </b>
  </summary>
  <p>
  </p>
  You need to call the purging macros ( LINE_PURGE or VORON_PURGE ) either in your slicer start gcode, or as part of your PRINT_START macro. Be sure to place these macros at the end, so it is called right before the print starts, and the nozzle has been fully heated.
</details>

## Credits:

KAMP was not a one man effort, it was made possible with help from fine folks such as:
- [MapleLeafMakers](https://github.com/MapleLeafMakers) - for assisting in the inception of this project.
- [Julian Schill](https://github.com/julianschill) - A true code warrior and jinja ninja.
- [frank.af](https://github.com/kageurufu) - For spearheading object cancellation in Klipper, and helping make this possible.
- [Le0n](https://github.com/leanghoun) - For the fantastic KAMP artwork.
- [Yenda](https://github.com/jtrmal) - For bringing KAMP into the realm of Moonraker's Update Manager.

- [You](https://en.wikipedia.org/wiki/You) - For the continued support and sharing KAMP with your friends. :smiling_face_with_three_hearts:

## You may also like...

- [Ellis' Print-Tuning Guide](https://ellis3dp.com/Print-Tuning-Guide/) - A guide for tuning your 3D printer to *perfection*.
- [Jontek2's Print_Start Guide](https://github.com/jontek2/A-better-print_start-macro) - A fantastic `Print_Start` guide for Voron printers.
- [Takuya's Tools](http://tools.takuya.wtf/index.html) A collection of handy tools for any Klipper user.

---



