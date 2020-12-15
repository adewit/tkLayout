1. [Does tkLayout works correctly if I set (and recompile) magnetic field to 3.5 T (trunk/include/global_constants.h)?](#faq1)
2. [Is there a way to have an asymetric tracker layout ?](#faq2)
3. [What is the meaning of each Red/Black/Blue markers on tracking resolution plots?](#faq3)
4. [What does "dsRotation" do?](#faq4)
5. [Is there a way to tilt sensors easily (in stead of writing in a csv file) ?](#faq5)
6. [Is it possible to align Vertex detector specifically (like half Circular + half Elliptical) ?](#faq6)
7. [What are the differences in between "_MS000" and "_noMS000" tags?](#faq7)
8. [Considering "transverse momentum error", what are the differences between the different tabs "Resolution (...)" ?](#faq8)
9. [What does "etaCut" do?](#faq9)
10. [For EndCaps, I could not manage to set innerRadius to xxx mm. What am I missing ?](#faq10)
11. [The resolution of track parameters are given for muons. How to do the same evaluation for different type of particles?] (#faq11)
12. [Is there a way to view constructed geometry in 3D (like Draw("ogl"))?](#faq12)
13. [What is a "stub" in tkLayout?](#faq13)
14. [How do I define stub-finding modules ("pt modules")](#faq14)
15. [What is the meaning of the three plots with "number of hits" in the geometry page?](#faq15)
16. [What is the meaning of each markers in the plots with "number of hits" in the geometry page?](#faq16)
17. [I have trouble compiling tkLayout on my MacOs. What can I do?](#faq17)
18. [What is the current supported/recommended g++ compiler version?](#faq18)

<a name="faq1"/>
##### Does TkLayout works correctly if I set (and recompile) magnetic field to 3.5 T (trunk/include/global_constants.h)?
Yes. Another way of dealing with it would be to rescale the pt resolution by 3.8/3.5

<a name="faq2"/>
##### Is there a way to have an asymetric tracker layout?
Not at the moment. Changing the disk placement algorithm, anyway should be feasible. Additionally you would have to modify all the performance assessment plots to have eta instead of |eta| as x axis (if you make it switchable I would be glad to import this into tkLayout main code).

Of course, the simplest way of dealing with it would be to build two different configuration files (LHeC_plus and LHeC_minus) and then stitch all the performance plots together, after flipping the x axis of one of them, which can be easily done with a ROOT script

<a name="faq3"/>
##### What is the meaning of each Red/Black/Blue markers on tracking resolution plots?
pT = 1, 10, 100 GeV -- the values specified in the .tkgeometryrc main configuration file

<a name="faq4"/>
##### What does "dsRotation" do?
dsRotation != 0 (combined with nSensors=2) means that the pair of sensors in a module is twisted one with respect to another to give a (small) hatching angle between the strips. This makes them "stereo" modules, that is, by combining the hits on both sensors you can measure r/phi and z for each hit.

<a name="faq5"/>
##### Is there a way to tilt sensors easily (in stead of writing in a csv file) ?
If you want something like the 'tilted' barrel, the only way for now is using the .csv file, but we will (hopefully soon) create a semi-automated way of placing tilted modules in an optimal way.

<a name="faq6"/>
##### Is it possible to align Vertex detector specifically (like half Circular + half Elliptical) ?
Not at the moment, this would require some help from a developer interested in this kind of geometry.

<a name="faq7"/>
##### What are the differences in between "_MS000" and "_noMS000" tags?
They show the resolution expected considering multiple scattering and ignoring it (respectively).

<a name="faq8"/>
##### Considering "transverse momentum error", what are the differences between the different tabs "Resolution (...)" ?
Each module was assigned a 'tracking tag'. That is a DAQ path. In the default (CMS) example we can read out the pixel alone, or the outer tracker alone (i.e. trigger) or everything together. Tracking resolution for each of these cases is computed (taking into account all the multiple scattering from the whole tracker, of course)

<a name="faq9"/>
##### What does "etaCut" do?
After all modules are placed inside each section, all modules whose barycenter exceed eta=etaCut are removed.

<a name="faq10"/>
##### For EndCaps, I could not manage to set innerRadius to xxx mm. What am I missing ?
If you build endcaps with square detectors, there is a geometric reason why you cannot set an innerRadius to any specific value. You can indeed say how many rings of detectors you want, then the detectors are places hermetically and then you end up with a given minimum radius.

If you need to "adjust" the positions, you can add some extra overlap between two rings ("ringGap -34" in a ring would for example "push" that ring outward by 34 mm together with all rings inside it) or insert a gap between rings ("ringGap 34"), losing hermeticity at that location.

<a name="faq11"/>
##### The resolution of track parameters are given for muons. How to do the same evaluation for different type of particles?
Yes, resolution is for muons. At the moment these are the only particles evaluated, because it is simple to do so: the tracking resolution is computed using only the detectors spacial resolution and the multiple scattering. This correspond to the resolution expected for a high-energy muon. To evaluate the resolution for any other kind of particle, a more complex analysis would have to be done.

<a name="faq12"/>
##### Is there a way to view constructed geometry in 3D ? ( like Draw("ogl") )
There was in the beginning, but it was cumbersome to deal with and it was later dropped to favor a clearer presentation in 2D only.

<a name="faq13"/>
##### What is a "stub" in tkLayout?
In the CMS HL-LHC upgrade we have detector modules with two sensors and front-end electronics able to read both sensors in parallel. By matching the hits in the two (closely spaced) sensors the front-end electronics will be able to identify hits from high-pt tracks (with a pt cut aorund 2 GeV/c).

<a name="faq14"/>
##### How do I define stub-finding modules ("pt modules")?
  These modules are called "pt" modules in CMS jargon. In tkLayout a pt module is defined by having `nSensors`==2 and the `dsRotation`==0.

  The distance between the sensors is defined by the parameter dsDistance and the high-pt selection window is defined by the parameter `triggerWindow`.

  Sensors may be segmented along the local y axis (if `nSegments` is set to a value different from the default value 1). I this case it is assumed that stubs are found only if a hit is generated in the same segment of the two sensors. For example if `nSegments`==2 this represent the fact that two lines of strips are read from the sides of the modules by two different set of front-end electronics which cannot communicate between them.

  This behavior can be changed by setting `zCorrelation` to `multisegment` instead of the default value of `samesegment` 

<a name="faq15"/>
##### What is the meaning of the three plots with "number of hits" in the geometry page?
There are three similar plots describing the number of hits you will get with one completely straight track.

1. number of modules with at least one hit (self-explanatory)
1. number of hits (i.e. number of sensors with a hit)
1. number of modules with at least one stub

Number of pt-modules that are hit by the straight track with a geometry that allows the module (in principle) to identify a "stub"

<a name="faq16"/>
##### What is the meaning of each markers in the plots with "number of hits" in the geometry page?
The colored markers count hits or stubs in modules with the corresponding module type. The black markers are the total number of hits.

<a name="faq17"/>
##### I have trouble compiling tkLayout on my MacOs. What can I do?
Unfortunately MacOs is not a supported target architecture. tkLayout will work both on recent ubuntu distribution and on the Scientific Lunux distrubution currently deployed on lxplus (SLC6 at the time of writing).

In principle there is nothing preventing tkLayout to compile and run on MacOs, so if you find a solution to this problem we will be happy to include it in this FAQ list :-)

<a name="faq18"/>
##### What is the current supported/recommended g++ compiler version?
  We currently all use gcc 4.8 on the ubuntu distribution and and gcc 4.7 on Scientific Linux 6.
  Anyway, if you run on lxplus the correct compiler will always be used after running `source setup_slc6.sh` (while using a bash shell).
