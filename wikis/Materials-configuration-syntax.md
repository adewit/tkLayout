Each module type have is configuration file inside _config/stdinclude/Materials/_ folder, the structure of those files is made like a tree with:
  * **Component** for nodes;
  * **Element** for leaves.
The configuration files are read by the program and instances of _MaterialObject_ are created that respect the tree structure.

the properties are collected and inherited inside the elements (a property define in a component is applied to all elements under it), those properties are:
  * **componentName** the name of the inner component that contain the element (for instance _Sensor_);
  * **elementName** the name of the element (for instance _SenSi_);
  * **quantity** the amount of material accordingly to the specified unit;
  * **unit** the unit of measure of the quantity, can be **g** for a fixed amount of grams, **g/m** for a quantity multiplied by a length, **mm** for a quantity multiplied by a density and an area (0.2mm of a Si in a module is equal to the area of the module times 0.2 times the area of the module);
  * **scale** if is true means that the material need to be multiplied by the number of segments and strips;
  * **service** if is true means that the material is not of the module but is a service that exit from it;
  * **nStripsAcross** the number of strips for calculating the scaling;
  * **nSegments** the number of segments for calculating the scaling.
