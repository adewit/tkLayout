There is now the possibility, within tkLayout, to generate tilted layers whose modules are automatically placed.  

On that purpose, several geometrical constraints can be defined as input, in the cfg files.  
When parameters **isTilted** and **isTiltedAuto** are set to _true_, a tilted layer with automatic modules placement is constructed.  
After computation, the geometry can be found on tkLayout website, on the geometry tab. The geometry plots, and table _tilted layer with automatic placement : additional info_ are notably of interest.    

Below is explained how to use this tkLayout's new feature, from the cfg file point of view. All parameters that play a role in the tilted layer construction process are described.


### _Tracker_ and _Barrel_ blocks

These blocks' geometrical parameters are already described in https://github.com/tkLayout/tkLayout/wiki/Configuration-files . Below are further explanations for a few of them, which are related to the specific case of a tilted layer with automatic placement.

##### Parameters :

* **zError** : zError is only used for the _flat part_.

* **bigDelta** : bigDelta is only used for the _flat part_.

* **zOverlap** : zOverlap. For the _flat part_, the most stringent between zError and zOverlap is used. For the tilted rings, only zOverlap is used.

* **phiOverlap** : phiOverlap should not be set for a tilted layer with automatic placement.  
Indeed, **numRods** is set in the cfg file for a tilted layer with automatic placement, which already gives a geometrical constraint !

* **smallParity** : smallParity value will not be taken into account for a tilted layer with automatic placement. Indeed, smallParity will be calculated so that the last module in the flat part is at -smallDelta.

* **smallDelta** : smallDelta is only used for the _flat part_.

* **maxZ** : maxZ should not be specified, since modules are placed automatically.

* **numModules** : numModules should not be set for a tilted layer with automatic placement.  
Indeed, setting **numModulesFlat** and **numModulesTilted** will already give all the relevant information !

* **startZMode** : mandatory set to _modulecenter_ for a tilted layer with automatic placement.  

* **innerRadius** : radius for innermost layer in the _flat part_.

* **placeRadiusHint** : radii for layers located between innermost layer and the outermost layer in the _flat part_.

* **outerRadius** : radius for the outermost layer in the _flat part_.

* **phiSegments** : phiSegments should not be set for a tilted layer with automatic placement.  
Indeed, **numRods** is set in the cfg file for a tilted layer with automatic placement, which already gives a geometrical constraint !



### _Layer_ block : additional parameters

This block should look more or less like this :

Layer _layerNumber_ {

> isTilted = _value_;

> isTiltedAuto = _value_;

> numModulesFlat = _value_;

> numModulesTilted = _value_;

> numRods = _value_;

}

##### Parameters :

  * **isTilted** :
    * type : bool
    * meaning : is the layer tilted ?
    * mandatory : no
    * default value : _false_

  * **isTiltedAuto** :
    * type : bool
    * meaning : will the tilted layer use an automatic modules placement ?  
If **isTiltedAuto** is set to _false_, the placement of modules within a tilted layer is not automatic. If so, positions and tilt angles values should be manually set in the csv file : tkLayout/geometries/CMS_Phase2/OuterTracker/Tilted/short_layeri.csv  
If **isTiltedAuto** is set to _true_, the placement of modules within a tilted layer is automatic.
    * mandatory : yes (if **isTilted** = _true_)
    * default value : _none_

  * **numModulesFlat** :
    * type : int
    * meaning : number of Z+ modules in the flat part of the tilted layer, for one rod. This should include the central module.
    * mandatory : yes (if **isTilted** = _true_ and **isTiltedAuto** = _true_)
    * default value : _none_

  * **numModulesTilted** :
    * type : int
    * meaning : number of Z+ modules in the tilted part of the tilted layer, for one rod
    * mandatory : yes (if **isTilted** = _true_ and **isTiltedAuto** = _true_)
    * default value : _none_

  * **numRods** :
    * type : int
    * meaning : number of rods within the tilted layer
    * mandatory : yes (if **isTilted** = _true_ and **isTiltedAuto** = _true_)
    * default value : _none_



### _Ring_ block : additional parameters

This block should look more or less like this :

Ring _ringNumber_ {

> ringInnerRadius = _value_;

> ringOuterRadius = _value_;

> tiltAngle = _value_;

> theta_g = _value_;

> zOverlap = _value_;

}

##### Parameters :

  * **ringInnerRadius** :
    * type : double
    * meaning : radius of the centers of the modules located in the inner part of the ring
    * mandatory : yes (if **isTilted** = _true_ and **isTiltedAuto** = _true_)
    * default value : _none_

  * **ringOuterRadius** :
    * type : double
    * meaning : radius of the centers of the modules located in the outer part of the ring
    * mandatory : yes (if **isTilted** = _true_ and **isTiltedAuto** = _true_)
    * default value : _none_

  * **tiltAngle** :
    * type : double
    * meaning : tilt angle (same for all modules of the tilted ring)
    * mandatory : yes (if **isTilted** = _true_ and **isTiltedAuto** = _true_)
    * default value : _none_

  * **theta_g** :
    * type : double
    * meaning : angle of the line joining the module centers of an inner module and an outer module
    * mandatory : yes (if **isTilted** = _true_ and **isTiltedAuto** = _true_)
    * default value : _none_

  * **zOverlap** :
    * type : double
    * meaning : overlap in Z, seen from the Z axis
    * mandatory : yes (if **isTilted** = _true_ and **isTiltedAuto** = _true_)
    * default value : _none_


##### Tilted ring projection in (RZ) plane :

![](https://ghugo.web.cern.ch/ghugo/layouts/wiki/pics/tilted_ring.png)
<p align="center">
  Figure 1 from [1]
</p>

The following parameters are represented in Figure 1 :  
ringInnerRadius : R1  
ringOuterRadius : R2  
theta_g : indicated in blue




### Bonus : template OT cfg file, with automatic tilted modules placement in TBPS

[OT_autoPlacement.cfg](https://ghugo.web.cern.ch/ghugo/layouts/wiki/texts/OT_autoPlacement.cfg)

Enclosed can be found an OT cfg file, whose TBPS barrel uses the automatic tilted modules placement.  
It has been created from [OT.cfg](https://ghugo.web.cern.ch/ghugo/layouts/wiki/texts/OT_static.cfg) , which is the tilted OT used so far in tkLayout, with static modules positioning in TBPS.  
numModulesFlat, numModulesTilted, numRods values (for the tilted layers), and ringInnerRadius, ringOuterRadius, tiltAngle values (for the tilted rings), have been inspired from the static values, that were used so far for the tilted. theta_g and z_overlap have been chosen more 'randomly'.  
Of course, this is just a basis to play with !




### Reference

Thanks a lot to Duccio. This work is based from [1]. 
 
[1] : Abbaneo D. (2013). Tilted geometry for PS Tracker.