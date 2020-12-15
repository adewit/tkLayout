DetId schemas used in CMSSW_8_1_X are presented at Erica's page : https://github.com/cms-sw/cmssw/blob/CMSSW_8_1_X/Geometry/TrackerNumberingBuilder/README.md  
Similarly, a DetId schema has been implemented in tkLayout for the entire Tracker.  
DetIds in tkLayout are matching DetIds in CMSSW (CMSSW_8_1_X, Phase 2).

The relevant DetId schemas are reminded below.


## DetId schemes (CMSSW_8_1_X Phase 2)
* Subdetector 5 DetId Scheme (Phase 2 Outer Tracker Barrel)

<table>
  <tr>
    <td> <b> Name </b> </td>
    <td> <b> Start bit </b> </td>
    <td> <b> Size <br> (number of bits) </b> </td>
    <td colspan="3"> <b> Value </b> </td>
  </tr>

  <tr>
    <td> Tracker </td>
    <td> 28 </td>
    <td> 4 </td>
    <td colspan="3"> always 1 </td>
  </tr>

  <tr>
    <td> OT Barrel </td>
    <td> 25 </td>
    <td> 3 </td>
    <td colspan="3"> always 5 <br> (OT Barrel is identified by 205 [100] ≡ 5) </td>
  </tr>

  <tr>
    <td> <i> not used </i> </td>
    <td> 24 </td>
    <td> 1 </td>
    <td colspan="3"> always 0 </td>
  </tr>

  <tr>
    <td> Layer </td>
    <td> 20 </td>
    <td> 4 </td>
    <td colspan="3"> in increasing r, starting from 1 </td>
  </tr>

  <tr>
    <td> Category </td>
    <td> 18 </td>
    <td> 2 </td>
    <td> Tilted rings on Z- side: <br> 1 </td>
    <td> Tilted rings on Z+ side: <br> 2 </td>
    <td> Flat layer, or Flat part of a tilted layer: <br> 3 </td>
  </tr>

  <tr>
    <td> Ring / Rod </td>
    <td> 10 </td>
    <td> 8 </td>
    <td colspan="2"> in increasing rings' average z, starting from 1 </td>
    <td> in increasing rods' phi, starting from 1 <br> (1 for the smallest phi > 0) </td>
  </tr>

  <tr>
    <td> Module </td>
    <td> 2 </td>
    <td> 8 </td>
    <td colspan="2"> in increasing phi, starting from 1 <br> (1 for the smallest phi > 0) </td>
    <td> in increasing z, starting from 1 </td>
  </tr>

  <tr>
    <td> Module type </td>
    <td> 0 </td>
    <td> 2 </td>
    <td colspan="3"> <li> lower sensor (for example for PS module, P sensor): 1 </li> <li> upper sensor (for example for PS module, S sensor): 2 </li> <li> pair of sensors: 0 </li> </td>
  </tr>
</table>


* Subdetector 4 DetId Scheme (Phase 2 Outer Tracker Endcaps)

<table>
  <tr>
    <td> <b> Name </b> </td>
    <td> <b> Start bit </b> </td>
    <td> <b> Size <br> (number of bits) </b> </td>
    <td> <b> Value </b> </td>
  </tr>

  <tr>
    <td> Tracker </td>
    <td> 28 </td>
    <td> 4 </td>
    <td> always 1 </td>
  </tr>

  <tr>
    <td> OT Endcaps </td>
    <td> 25 </td>
    <td> 3 </td>
    <td> always 4 <br> (OT Endcaps is identified by <br> 204 [100] ≡ 4) </td>
  </tr>

  <tr>
    <td> Z side </td>
    <td> 23 </td>
    <td> 2 </td>
    <td> <li> Z- side: 1 </li> <li> Z+ side: 2 </li> </td>
  </tr>

  <tr>
    <td> <i> not used </i> </td>
    <td> 22 </td>
    <td> 1 </td>
    <td> always 0 </td>
  </tr>

  <tr>
    <td> Disk </td>
    <td> 18 </td>
    <td> 4 </td>
    <td> in increasing abs(z), starting from 1 </td>
  </tr>

  <tr>
    <td> Ring </td>
    <td> 12 </td>
    <td> 6 </td>
    <td> in increasing r, starting from 1 </td>
  </tr>

  <tr>
    <td> <i> not used (ex-Panel) </i> </td>
    <td> 10 </td>
    <td> 2 </td>
    <td> always 1 </td>
  </tr>

  <tr>
    <td> Module </td>
    <td> 2 </td>
    <td> 8 </td>
    <td> in increasing phi, starting from 1 <br> (1 for the smallest phi > 0) </td>
  </tr>

  <tr>
    <td> Module type </td>
    <td> 0 </td>
    <td> 2 </td>
    <td> <li> lower sensor (for example for PS module, P sensor): 1 </li> <li> upper sensor (for example for PS module, S sensor): 2 </li> <li> pair of sensors: 0 </li> </td>
  </tr>
</table>


* Subdetector 1 DetId Scheme (Phase 2 Inner Tracker Barrel)

<table>
  <tr>
    <td> <b> Name </b> </td>
    <td> <b> Start bit </b> </td>
    <td> <b> Size <br> (number of bits) </b> </td>
    <td> <b> Value </b> </td>
  </tr>

  <tr>
    <td> Tracker </td>
    <td> 28 </td>
    <td> 4 </td>
    <td> always 1 </td>
  </tr>

  <tr>
    <td> IT Barrel </td>
    <td> 25 </td>
    <td> 3 </td>
    <td> always 1 <br> (IT Barrel is identified by 201 [100] ≡ 1) </td>
  </tr>

  <tr>
    <td> <i> not used </i> </td>
    <td> 24 </td>
    <td> 1 </td>
    <td> always 0 </td>
  </tr>

  <tr>
    <td> Layer </td>
    <td> 20 </td>
    <td> 4 </td>
    <td> in increasing r, starting from 1 </td>
  </tr>

  <tr>
    <td> Rod </td>
    <td> 12 </td>
    <td> 8 </td>
    <td> in increasing rods' phi, starting from 1 <br> (1 for the smallest phi > 0) </td>
  </tr>

  <tr>
    <td> Module </td>
    <td> 2 </td>
    <td> 10 </td>
    <td> in increasing z, starting from 1 </td>
  </tr>

  <tr>
    <td> Module type </td>
    <td> 0 </td>
    <td> 2 </td>
    <td> always 0 </td>
  </tr>
</table>


* Subdetector 2 DetId Scheme (Phase 2 Inner Tracker Endcaps)

Nearly same scheme as for Subdetector 4.  
Only one difference : IT Endcaps is identified by 202 [100] ≡ 2.


## **Great ! But HOW TO ... Get DetIds with tkLayout**

**1)** **Specify the DetId scheme names in the configuration files (at the Outer Tracker / Inner Tracker hierarchy level)**.  
For example, for the Outer Tracker, in order to use the schemes presently available :  
``` 
Tracker Outer {
  barrelDetIdScheme Phase2Subdetector5
  endcapDetIdScheme Phase2Subdetector4
}
```
For example, for the Inner Tracker, in order to use the schemes presently available :  
``` 
Tracker Inner {
  barrelDetIdScheme Phase2Subdetector1
  endcapDetIdScheme Phase2Subdetector4
}
```
**2) Within tkLayout, DetIds are then accessible in an intuitive way** : 
- for each DetectorModule m :  with **m->myDetId()**.
- for each Sensor s :  with **s->myDetId()**.

NB: Please check the 'Warning' tab on the website !! 
In case of problem, there might be useful information in there.

**3) On tkLayout's website, DetIds are accessible on the geometry and info tabs**.   
Please look at the csv files at the bottom of the web pages.  
