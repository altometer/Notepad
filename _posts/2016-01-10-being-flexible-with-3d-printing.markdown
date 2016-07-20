---
published: true
title: Being Flexible with 3D printing.
layout: post
tags: [3DPrinting, NinjaFlex, 3DprintingExperiments]
---
### Being Flexible with 3D printing.

Around November, I was contacted be a community leader from [FennerDrives.](http://www.fennerdrives.com/) For those who don't know, Fenner Drives is the parent company of the highly popular flexible 3D printing filament known as [NinjaFlex](http://www.ninjaflex3d.com/). This filament has quite a few problems, namely speed and difficulty in use.


After a few months of talking and signing a few NDAs, I was shipped a box with a few beta filament rolls of the new filament advertised and being released as [Cheetah Filament](http://www.ninjaflex3d.com/products/cheetah/) Cheetah filament is designed for use to ease the entry barrier of printing with flexible filaments. Aiming at printing temperatures close to ABS with no heated bed needed. recommended testing speeds are 50-70mm/s² or, double to triple the speed of normal NinjaFlex.

### Testing
The filament is somewhat stretchy before being melted. It has a grainy texture to it, extremely similar to uncooked spaghetti noodles. After printing, it has a smooth finish and appears to be significantly more bendable and stretchy. I did notice that if I leave my hotend turned up to printing temperatures with the filament loaded, it seems to bubble and boil out of the nozzle.

Below is a table of the numerous prints I completed.

| Print     | Infill | Speeds  | Notes    |   
| :-------------: |:-------------: |:-------------: |:-------------: |
| [Polygon Pikachu](http://www.thingiverse.com/thing:376601)| 10%,  3D Hexagon | 40 mm/s² | The models' ears came out pretty jagged. The tail is an utter mess. Any model with a small base allowing for much movement in the upper layers will have its problems enhanced severalfold due to the flexibility of the filament (no duh in retrospect.)|
| [Polygon Charmander](http://www.thingiverse.com/thing:323038) @ 220% scale. | 5%, 3D hexagon | 45 mm/s² | I am quickly finding the 3D hexagon infill to be way too strong even for flexible models. I figured having a bigger print would show off the squish way more, but it's still fairly stiff.
| Pencil cup | 0% | 50 mm/s² | A single-walled "Twisted Vase" style print. This went by extremely fast and I am extremely pleased with the results. The walls are so flexible they are flimsy! Layer adhesion is so good that I can actually stretch the filament in multiple directions.
| [Ninja Throwing Star](http://www.thingiverse.com/thing:105208) | 10%, Rectilinear | 50 mm/s² | Wanting to test overhangs and try again with infill. This print came out spectacularly. I threw it around the house a few times and tested bending it to various shapes.
| [Celtic Skull](http://www.thingiverse.com/thing:29114) @ 50% scale. | 5% Rectilinear | 50 mm/s² | Despite using 4 top-layers, there were severe adhession issues. The roof of this print is full of holes. I'd like to attribute this to the steepness of a dome, [but am unable to conclude such.
| [Octopus says hello](http://www.thingiverse.com/thing:27053) | 0% | 55 mm/s² | The goal of this print, was to create as squishy of a toy as possible. I upped it to 3 perimeters, 5 bottom layers @ 0.2mm and 6 top layers, but the top was still full of holes. Looking at Slic3r's config, it's not properly generating support for the layers as needed. (Yes, extra perimeters if needed is turned on)
| A simple Box and lid. | 10%, Rectilinear | 55 mm/s² | Currently Printing.

A recurring theme in all of these prints is the speed. My Folgertech 2020 starts experiencing resonance issues past 55mm/s x and y movespeeds. I have upgrades in the future planned to remediate this, but for the time being I have to deal with it.

Further tests will be done on my mini-kossel w/ bowden tube.
