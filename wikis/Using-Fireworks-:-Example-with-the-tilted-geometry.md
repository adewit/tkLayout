Please note that general reference on Fireworks can be found at https://twiki.cern.ch/twiki/bin/view/CMSPublic/WorkBookFireworks .

We will work on CMSSW_6_2_SLHCDEV_X branch.
The workflow is as follows:  
 
**1) Getting started**

    ssh lxplus
    cmsrel CMSSW_6_2_SLHCDEV_X
    cd CMSSW_6_2_SLHCDEV_X/src
    cmsenv
    git cms-addpkg Geometry/TrackerCommonData
    git cms-addpkg Geometry/CMSCommonData
    git cms-addpkg Fireworks/Geometry
    scram b -j8


**2) NB : Where tilted geometry xml files are located**

The set of xml files from tkLayout for Baseline2015_tilted are located at :

    src/Geometry/TrackerCommonData/data/PhaseII/Baseline2015_tilted
Paths to these xmls are mentioned in :

    src/Geometry/CMSCommonData/python/cmsExtendedGeometryPhase2Baseline2015TiltedXML_cfi.py

xml files & paths can of course be modified at your convenience.


**3) Run Fireworks**

Before running Fireworks, please of course edit

    src/Fireworks/Geometry/python/cmsShowFF_displayGeom_cfg.py

replacing :

    process.load("Geometry.CMSCommonData.cmsIdealGeometryXML_cfi")

by :

    process.load("Geometry.CMSCommonData.cmsExtendedGeometryPhase2Baseline2015TiltedXML_cfi")

NB : If you want to work on another set of xml files, please of course edit accordingly (see part 2).

Then :

**a) To visualize the geometry** :

    cmsShowFF -c $CMSSW_RELEASE_BASE/src/Fireworks/Core/macros/simGeo.fwc Fireworks/Geometry/python/cmsShowFF_displayGeom_cfg.py

**b) To check for extrusions/overlaps** :

    cmsShowFF -c $CMSSW_RELEASE_BASE/src/Fireworks/Core/macros/overlaps.fwc Fireworks/Geometry/python/cmsShowFF_displayGeom_cfg.py
