# Contents

* [General](#general)
  - [Property inheritance](#property-inheritance)
* [Layout file options](#layout-file-options)
  - [Overview](#1-overview)
  - [Top-level blocks](#2-top-level-blocks)
    * [Simulation parameters](#21-simparms)
    * [Tracker](#22-tracker)
  - [Tracker blocks](#3-tracker-blocks)
    * [Barrel tracker](#31-barrel)
    * [End-cap tracker](#32-end-cap)
  - [Modules description](#4-modules-description) 
  - [Services description](#5-services-description)
* [Material description](#material-description)
  - [Global material list](#1-the-global-material-list-mattablist)
* [Other](#other)
  - [Irradiation map](#1-map-of-total-radiation-irradiationmap)

# General
The main configuration file of tkLayout is usually left unchanged after the initial setup. The default path of this configuration file is $HOME/.tkgeometryrc, but another file will be loaded if the environment variable TKGEOMETRYRC is set. Here is a typical definition of the config file (which has a bash-like syntax).

     TKG_LAYOUTDIRECTORY="/home/user/www/layouts"
     TKG_STANDARDDIRECTORY="/home/user/tkLayout"
     TKG_MOMENTA="1.00, 10.00, 100.00"
     TKG_TRIGGERMOMENTA="2.00, 5.00, 10.00"
     TKG_THRESHOLD_PROB="0.50, 0.90, 0.95"
     TKG_BINDIRECTORY="/home/user/bin"
Most important here is the definition of TKG_LAYOUTDIRECTORY (where tkLayout creates the mini-website as an output) and TKG_STANDARDDIRECTORY, where the configuration files are supposed to be (and the code is too, for developers).

A few configuration files which are not supposed to be modified by the users are in the $TKG_STANDARDDIRECTORY/config directory:
* irradiation.map
* irradiationPixel.map
* mattab.list
* stdinclude/ (directory)

When analyzing a given layout, tkLayout runs on a single configuration file describing the geometry and the material model. This in turn can (and often does) include other configuration files with the `@include` and `@includestd` directives. The first one will include configuration files with the relative path from the original including file and the latter one will look for configuration files in $TKG_STANDARDDIRECTORY/config/stdinclude as defined by the TKGEOMETRYRC file

## Property inheritance
See presentation by Giovanni Bianchi at [tkLayout: configuration files structure](https://tklayout.web.cern.ch/sites/tklayout.web.cern.ch/files/tklayout_tech_notes.pdf).

Properties are inherited from a larger container into a smaller one.
In general the more specific information overrides the less specific one. Only when the same exact parameter is specified twice, then the latest definition trumps over the earliest one.

There are cases where the inheritance pattern might not be obvious. One example of this is when the parameters for a given Ring are assigned to a Tracker section (for example an Endcap) *and also* to a given Disk and a specific Ring in a Disk.

For example:

```
plotColor 3
Ring 1 { plotColor 1 } // Ring 1 (any layer) should be Blue
Layer 1 { plotColor 2 } // Layer 1 should be Red (apart from Ring one, which I said should be Blue)
Layer 2 {
  plotColor 5 // Layer 2 should be Red (apart from Ring one, which I said should be Blue)
  Ring 5 {
    plotColor 6 // Ring 5 of Layer 2 should be Brown (trumps over the generic Layer 2 color)
  }
}
Ring 5 { plotColor 1 } // Ring 5 (any layer) should be Blue... but not for Layer 2, where the Ring color is specified in detail!
```
Generates the following colour pattern:
![img000](https://cloud.githubusercontent.com/assets/584822/10394427/89b31318-6e97-11e5-9868-200e23a0fd94.png)

In case the very same parameter is assigned twice, the latest counts, as in the following example:
```
plotColor 3
Ring 1 { plotColor 1 } // Ring 1 (any layer) should be Blue
Layer 1 { plotColor 2 } // Layer 1 should be Red (apart from Ring one, which I said should be Blue)
Layer 2 {
  plotColor 5 // Layer 2 should be Red (apart from Ring one, which I said should be Blue)
  Ring 5 {
    plotColor 6 // Ring 5 of Layer 2 should be Brown (trumps over the generic Layer 2 color)
  }
}
Ring 5 { plotColor 1 } // Ring 5 (any layer) should be Blue... but not for Layer 2, where the Ring color is specified in detail!
Ring 7 { plotColor 1 } // Ring 7 (any layer) should be Blue
Ring 7 { plotColor 5 } // ... hold on: I changed my mind: Ring 7 (any layer) should be Brown
```
Which generates the following colour pattern:
![img000](https://cloud.githubusercontent.com/assets/584822/10394647/da27bf28-6e98-11e5-8dc2-683914794751.png)

A similar code for the end-cap produces the same result:
```
plotColor 3
Ring 1 { plotColor 1 }
Disk 1 { plotColor 2 }
Disk 2 {
  plotColor 5
  Ring 5 {
          plotColor 6
  }
}
Ring 5 { plotColor 1 }
Ring 7 { plotColor 1 }
Ring 7 { plotColor 5 }
```
![img000](https://cloud.githubusercontent.com/assets/584822/10394753/72bd9b54-6e99-11e5-9785-0dd19356fae5.png)

# Layout file options
This page details the options that can be specified in a layout configuration file.
##1. Overview
In tkLayout, a tracker layout (geometry + materials) is specified through one or more configuration files.
These files have a **block and property** based syntax with which all aspects of a tracker geometry can be defined.
A **block** is a set of other sub-blocks or properties enclosed by curly braces ({ }) and preceded by a _type tag_ and an _identifier_, such as:

    Tracker myTracker {
       containsOnly myBarrel
       Barrel myBarrel {…}
    }
Block types are alphanumeric and always start with a capital letter, while identifiers can also be numerals, or can even be empty, depending on the block type.

Blocks define objects which are created by tkLayout and assigned the properties defined within them. Where appropriate, a block can also contain other sub-blocks (such as Barrel blocks containing Layer blocks).
A **property** consists of a _name_ and a _value_, such as:
    smallDelta 3.05
    compressed true
    radiusMode shrink
Property names are alphanumeric and always start with a small letter, while values can be of any type (even lists of values), depending on the property. Properties have often a default value in tkLayout, so that if they are absent from the layout file they will fall back on their default value (properties without a default value that are not set in the layout file will cause an error).

In the configuration file **comments** start with // (double slash).
Lines do not need to end with a semicolon (trailing semicolongs are in fact ignored).
## 2\. Top-level blocks
tkLayout allows only two types of top-level blocks: **SimParms** and **Tracker**.
### 2.1 SimParms
The SimParms block ignores its identifier and defines parameters that affect the analyses.
Example definition:
 
    SimParms {
       numMinBiasEvents 140 // the number of minimum bias events to run with
       bunchSpacingNs 25    // the bunch spacing in ns. Used
       zErrorCollider 70    // see below
       
       // Parameters used by the power analysis
       numInvFemtobarns 3000      // number of fb-1 used to scale the fluence map
       operatingTemp -20          // detector operating temp (°C)
       referenceTemp 20           // reference temp (°C) at which the alpha parameter is calculated
       chargeDepletionVoltage 600 // charge depletion voltage (V)
       alphaParam 5.3e-17         // alpha parameter for power after irradiation
       
       // Parameters used by the trigger processors analysis
       numTriggerTowersEta 6 // no. of trigger processors in eta
       numTriggerTowersPhi 8 // no. of trigger processors in phi
       triggerPtCut 2        // min pT used for triggering (affects the size of the overlap areas in phi)
       triggerEtaCut 2.2     // max |eta| used for triggering
    }
Particularly important is the property `zErrorCollider` which specifies the sigma of the gaussian distribution of the Z coordinate of primary vertices (simulating the beam spot smear). When analyzing the coverage of a layout, tkLayout fires a number of tracks across eta and phi and determines their origin with a random gaussian distribution with sigma equal to zErrorCollider.
Note: only one `SimParms` block can normally be specified. In case more than one is found, their properties are <u>aggregated</u>.
### 2.2 Tracker
Tracker is a top-level block and, as its name implies, is the main block to define a tracker layout. tkLayout currently only supports a maximum of **two** Tracker objects. Furthermore, if two are present, one of them must be called **"Pixels"** (the Tracker object defining the inner pixel of CMS).
Example definition:

    Tracker myTracker {
       containsOnly myBarrel, myEndcap // see below
       Barrel myBarrel {…}
       Endcap myEndcap {…}
    }
**Barrel** and **Endcap** are inner blocks defining barrel and endcap sections. Note that while more than one Barrel block can appear in the same tracker, **only one ** endcap block is supported at the moment (this will be changed in a future release).
Relevant property: `containsOnly` when present, this property causes only the barrel and endcap blocks specified to be created, ignoring all other barrel/endcap declarations. This property is useful to quickly exclude one or more blocks, for debugging or selective analysis.

**General tracker parameters:**

* **bigDelta** - rod (ladder) +-shift in _R_ with respect to average layer position.
* **smallDelta** - module +-shift in _R_ with respect to rod average position.
* **bigParity** - start building rods (ladders) with _+bigDelta_ or _-bigDelta_, i.e. with parity equal to + or -, respectively.
* **smallParity** - start building modules with _+smallDelta_ or _-smallDelta_, i.e. with partiy equal to + or -, respectively.

## 3\. Tracker blocks
### 3.1 Barrel
Barrel section is denoted by a term ''Barrel'' and its unique name. Section starts, ends with {}. The Barrel section can only be defined inside a Tracker block.
Example definition:
 
    Barrel myBarrel {
       innerRadius 200 // inner radius of the barrel (mm)
       outerRadius 500 // outer radius of the barrel (mm)
       numLayers 3     // no. of Layers to create
       sameRods true   // Default is false, see below
       Layer 1 {…}
       Layer 2 {…}
       Layer 3 {…}
    }

The barrel layers are positioned at an average _radius_, where the radius is calculated based on various input parameters and optimization procedure to provide a full detector hermeticity. Each layer consists of individual rods (ladders), each rod then of several modules. Individual modules are positioned at _radius= Radius +- bigDelta_ (rod) _+- smallDelta_ (module). If a module consists of 2 sensors, the individual sensors are placed at _+- dsDistance/2_ with respect to the module's average radial position. A pair of matching hits in the two sensors is called a _stub_. The radii of innermost and outermost layers are implicitly fixed, but this can be released if needed. (The space for a detector is constrained by other detectors, e.g. calorimeter etc.) Layers in-between these two innermost and outermost layers are positioned at radii given by an optimization algorithm. First, the layers are positioned equidistantly and then, one of the following algorithms is applied depending on the _radiusMode_ parameter: fixed, auto, enlarge or shrink. _fixed_ stands for the positioning at a user-defined radii, i.e. fixed radii; _enlarge_ stands for an expansion, i.e. optimal radius is bigger then the current one and detectors are positioned to an expanded radius; _shrink stands for shrinking, i.e. optimal position is lower then the current one and detectors are positioned to a shrunk radius; _auto_ stands for an automatic mode, i.e. the closer configuration is chosen, either enlarge or shrink. That's the implicit build mode. In addition, modules are positioned such as all lines (at various _eta_) going from the primary vertex, defined as (0, 0, +-zErrorCollider), are always passing through layer modules. The positioning algorithm starts at Z=0 and takes then into consideration the extreme cases, taking into account all parameters: _bigDelta_, _smallDelta_, _zError_, _z/phiOverlap_, see further ...

Each layer is built in _+Z_ direction and then in _-Z_ direction. The first module can be either centered at _Z=0_ or it's edge can be centered at _Z=0_.
The length of the barrel is either defined by the desired number of modules in a half rod (_numModules_), or by the desired barrel half-length (_maxZ_).
The parameter _numModules_ represents either the number on _+Z_ side, including the central module if the fisrt module is placed at _Z=0_ and the number of modules on the _-Z_ side, excluding the
possible central module.
As the dimensions of modules are fixed by design, it happens that the last module (or even several last modules) might cross the maximum allowed distance in _Z_. To solve this issue all modules crossing this border as whole pieces are cut away (if parameter **allowCompressionCuts** is not set to false) and then, the compression algorithm is used. The difference between actual and allowed maximum _Z_ distance is expressed relatively to the allowed distance, and each module is shifted then by this ratio multiplied by its current position. To influence the building procedure, one might either set the number of modules or a maximum _Z_ distance. From engineering point of you, one might want to have the same type of rods in all layers. Then, when building the rods and simultaneously a requirement on covering the hermeticity is a need, one uses the optimal module position obtained from the innermost layer for modules built at _-smallDelta_ position and the module position obtained from the outermost layer for modules built at _+smallDelta_. Saying long story short, the extreme cases are always calculated and applied to be sure that hermeticity is conserved.

* **startZMode** - start building rod (ladder) modules at _Z=0_ by positioning module.
  - _modulecenter_ -   module centre at _Z=0_.
  - _moduleedge_ - module edge at _Z=0_.
* **barrelRotation** - start rod (ladder) positioning in _R-Phi_ at _Phi=barrelRotation_ [rad].
* **innerRadiusFixed** - innermost radius fixed (implicitly true), each module is positioned at radius equal to optimal radius _+-bigDelta+-smallDelta+-(dsDistance/2)_.
* **outerRadiusFixed** - outermost radius fixed (implicitly true), each module is positioned at radius equal to optimal radius _+-bigDelta+-smallDelta+-(dsDistance/2)_.
* **radiusMode** - _fixed_, _auto_, _enlarge_, _shrink_.
* **zOverlap** - required minimal module overlap in _Z_ direction [mm].
* **phiOverlap** - required minimal module overlap in _R-Phi_ [mm].
* **phiSegments** - required symmetry in _R-Phi_, e.g. 2 represents 180/180degs symmetry in _Phi_ angle, 4 represents 90/90/90/90degs symmetry.
* **numModule** - defines the number of modules in a half-rod (half-ladder). Either number of modules or maximum _Z_ distance can be specified.
* **maxZ** - defines maximum length in _+Z_ direction. Either number of modules or maximum _Z_ distance can be specified.
* **sameRods** - use the same rods (ladders) in all barrel layers? Implicitly being false.
* **compressed** - use a compressing algorithm to fit modules to _maxZ_ distance (avoid cutting modules at the edge). Implicitly being true.
* **forbiddenRange** - range in _Z_, e.g. 91-95 (91-95 mm), which represents a forbidden range in-between individual modules in _Z_ (necessary from engineering point of view). It can be positive or negative and the reason is e.g. to avoid that screw holes will be crossing each other etc.

**Comment:** The geometry layout plots show module projections, where each module is represented by a line (drawn using two module corners' coordinates, i.e. with radius equal to the edge of module, not the centre of the module). To cross-check module central positions, see **csv** data file (Geometry files) in web output **Info** section.

### 3.2 End-cap

# Material description

## 1. The Global Material List (mattab.list)

The global material list in _mattab.list_ contains one entry for each available material with some of its properties, namely _density_, _radiation length_ and _interaction length_. The name of this file _must not be changed_, but the user may add materials as needed. This is actually pretty likely, since every material used in a custom material configuration file _must_ also be listed here. The simple reason for this is that the only overlap between the two files are the material tags (which must match in order to cross-reference between the two). The rest of the information is not duplicated anywhere - but all of it is needed by the application.

The file can be found in the _config/_ subfolder of _tkmaterial_ and must not be moved or deleted.

An entry in _mattab.list_ looks fairly straightforward:

_tag density radiationlength interactionlength_
**i.e**
SenSi 2.33 21.82 108.4

The units are always the same, so they don't need to be written explicitly. They are **g/cm<sup>3</sup>** for the density and **g/cm<sup>2</sup>** for radiation and interaction length. The application converts back and forth between them and those in the material configuration file automatically.

# Other
## 1. Map of total radiation (irradiation*.map)
t.b.d. (obtained from output of the `[CMS FLUKA online tool](https://cms-project-fluka-flux-map.web.cern.ch/cms-project-fluka-flux-map/)` 
