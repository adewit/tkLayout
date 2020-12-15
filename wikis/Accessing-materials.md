For information on how to use the material configuration files see the [[corresponding User Manual section|Materials-configuration-syntax]]

## Reaching the modules and MaterialObjects

For reaching the modules is possible to define an online _Visitor_ class, for instance:
```
...
  class ModuleVisitor : public GeometryVisitor {
  public:
    ModuleVisitor() {}
    virtual ~ModuleVisitor() {}

    void visit(DetectorModule& module) {
      //do things with module
      ...
    }
  };

  ModuleVisitor visitor;
  tracker.accept(visitor);
...
```

Now you can access to the materials in two different ways.

### First method
When you have a reference or a pointer to a module you can access to the _MaterialObject_ calling:
```
module.materialObject();
```
When you have a reference to the _MaterialObject_ you can visit the objects of the hierarchy. A _MaterialObject_ contains a pointer to a _Materials_, a _Materials_ contain a vector of pointers to _Component_, a _Component_ contains a vector of pointers to other _Component_ (subcomponents) and a vector of pointers to _Element_. Summarizing:
  * materialObject (_MaterialObject_)
    * materials (_Materials`*`_)
      * components (_std::vector<const Component`*`>_)
        * components (_std::vector<const Component`*`>_)
        * materials (_std::vector<const Element`*`>_)
In the elements you can access to the properties defined in the configuration file doing:
```
element->componentName();
element->elementName();
element->quantity();
...
```
**please note** that if _service() == true_ means that the material is not of the module but is a service that exit from the module.

If you want the quantity in grams, not the one defined in the configuration file, you can call:
```
element->quantityInGrams(module);
```
The function need a reference to the module for knowing the dimension of the length or the area for converting the unit of measure.

**note that** the scaling is not took into account by the previous function, if you want the total quantity in grams and already scaled, call:
```
element->totalGrams(module)
```

### Second method
You can call the function of _DetectorModule_ (or also of the _MaterialObject_):
```
module.getLocalElements();
```
The returned value is of type _ElementsVector_ and is a vector of Elements defined in this way:
```
typedef std::vector<const MaterialObject::Element*> ElementsVector;
```
The vector is populated only with pointers to elements that are not services, or rather the locals elements of the module.

As already said in the previous section, in the elements you can access to the properties:
```
element->componentName();
element->elementName();
element->quantity();
...
```
and call the functions:
```
element->quantityInGrams(module);
element->totalGrams(module)
```