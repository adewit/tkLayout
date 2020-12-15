The workflow is as follows:  

**1) Generate XML files in tkLayout with an option "--xml"** 

**1 bis) Plug to 10-disk Pixel. This is now unnecessary !**  

Currently the pixel geometry is just copied from an old configuration (3 pixel discs). To add pixel discs, e.g. Pixel10D configuration, you have to add them by hand.  
This is necessary for the files that include pixel information (trackerStructureTopology.xml, trackersens.xml, trackerRecoMaterial.xml, trackerProdCuts.xml).  
  
**2) Getting started with cmssw : example with CMSSW_11_1_5** (more info : see http://cms-sw.github.io/faq.html)

    ssh lxplus  
    cmsrel CMSSW_11_1_5
    cd CMSSW_11_1_5/src  
    cmsenv  
    git cms-addpkg Geometry/TrackerNumberingBuilder  
    git cms-addpkg Geometry/TrackerGeometryBuilder  
    git cms-addpkg Geometry/TrackerCommonData  
    git cms-addpkg Geometry/CMSCommonData  
    git cms-addpkg Geometry/TrackerRecoData  
    git cms-addpkg Geometry/TrackerSimData  
    git cms-addpkg Configuration/Geometry  
    scram b -j 8  

**3) Copy the xml files into the corresponding directories in CMSSW**  


  
---- If you are not sure where to put your XMLs, try the following script in src/YOUR_CONF.  
Specify your configuration file at \<YOUR_CONF_WITHOUT_DOT_PY\>, e.g. step2_DIGI_L1_L1TrackTrigger_DIGI2RAW (note that you don't need ".py"). Run it as a python script.  

To generate a CMSSW config file for this purpose, can use one of the relVal workflowsd. Check available workflows for phase 2 with, e.g.:
`runTheMatrix.py --showMatrix --what relval_2026` (nb more workflows are available with `--what upgrade`, but they're not exclusively phase 2).
Picking one of them (for example `27434.0`) run: `runTheMatrix.py -l 27434.0 --command "-n 1"`

```python
#! /usr/bin/env python
# This script expands your configuration with dumpPython() and
# search the directory where you have to copy your XMLs.
import os,re
# load configuration file
import step2_DIGI_L1TrackTrigger_L1_DIGI2RAW_HLT as myconf
process = myconf.process

# xml list to be replaced
xmllist = [ "tracker.xml",
            "trackerStructureTopology.xml",
            "trackersens.xml",
            "trackerProdCuts.xml",
            "trackerRecoMaterial.xml",
            "otst.xml",
            "pixelsens.xml",
            "pixfwd.xml",
            "pixelProdCuts.xml",
            "pixbar.xml",
            "pixel.xml"]


# temporary output
tmpfilename = "out.tmp"
tmpfile = open(tmpfilename,"w")
tmpfile.write(process.dumpPython())
tmpfile.close()

tmpfile = open(tmpfilename,"r")
counter = 0
hasmissingpath=0
while tmpfile:
        confline = tmpfile.readline()
        for axml in xmllist:
                if re.search(axml,confline):
                        counter += 1
                        todir     = re.sub(axml,"",confline).split("'")[1]
                        if len(todir) > 0:
                            pkgdir    = todir.split("/")[0]
                            pkgsubdir = todir.split("/")[1]
                            print str(counter) +") Necessary package : " + pkgdir + "/" + pkgsubdir
                            print "Put your " + axml + " into " + todir
                            print ""
                        else:
                            hasmissingpath=1
                            print "Found " + axml + " in line" + confline + "but no path found"
        if confline=="":
                break

# clean up
if hasmissingpath==0:
    os.remove(tmpfilename)
```

Output example:  

```
Found tracker.xml in line        XMLFile = cms.string('tracker.xml'),
but no path found
2) Necessary package : Geometry/TrackerCommonData
Put your pixfwd.xml into Geometry/TrackerCommonData/data/PhaseII/TiltedTracker613_MB_2019_04/

3) Necessary package : Geometry/TrackerCommonData
Put your pixbar.xml into Geometry/TrackerCommonData/data/PhaseII/TiltedTracker613_MB_2019_04/

4) Necessary package : Geometry/TrackerCommonData
Put your otst.xml into Geometry/TrackerCommonData/data/PhaseII/TiltedTracker404/

5) Necessary package : Geometry/TrackerCommonData
Put your tracker.xml into Geometry/TrackerCommonData/data/PhaseII/TiltedTracker613_MB_2019_04/

6) Necessary package : Geometry/TrackerCommonData
Put your pixel.xml into Geometry/TrackerCommonData/data/PhaseII/TiltedTracker615/

7) Necessary package : Geometry/TrackerCommonData
Put your trackerStructureTopology.xml into Geometry/TrackerCommonData/data/PhaseII/TiltedTracker404/

8) Necessary package : Geometry/TrackerSimData
Put your trackersens.xml into Geometry/TrackerSimData/data/PhaseII/TiltedTracker404/

9) Necessary package : Geometry/TrackerSimData
Put your pixelsens.xml into Geometry/TrackerSimData/data/PhaseII/TiltedTracker404/

10) Necessary package : Geometry/TrackerRecoData
Put your trackerRecoMaterial.xml into Geometry/TrackerRecoData/data/PhaseII/TiltedTracker613_MB_2019_04/

11) Necessary package : Geometry/TrackerSimData
Put your trackerProdCuts.xml into Geometry/TrackerSimData/data/PhaseII/TiltedTracker404/

12) Necessary package : Geometry/TrackerSimData
Put your pixelProdCuts.xml into Geometry/TrackerSimData/data/PhaseII/TiltedTracker404/
```

In out.tmp you can check what the path for the XML files that are found but don't have the full path passed - in this case it was a location also prompted by points 2-12 so no need to act on it.

**4) If you changed the modules** (! Here is assumed that the xml you want are already incorporated !) I THINK THIS IS NO LONGER NEEDED

Thanks to Gaëlle for the following instructions, just copied here.  

This is to avoid this kind of error :

    %MSG-e TrackerSimInfoNumbering:  OscarProducer:g4SimHits  01-Jun-2015 15:46:15 CEST Run: 1 Event: 1  
     ERROR: DDD sensitive detectors do not match Geant4 ones.  
    %MSG

It is also useful to avoid Lorentz angle errors at step 3 :
  
    %MSG-e SiPixelLorentzAngle:  PixelTrackProducer:pixelTracks  03-Jun-2015 15:49:38 CEST Run: 1 Event: 1  
    SiPixelLorentzAngle for DetID 346879056 is not stored  
    %MSG

a)  

    cd CMSSW_6_2_0_SLHC25/src  
    cmsenv  
    git cms-addpkg SLHCUpgradeSimulations/Geometry  
    scramv1 b -j 8  
  
    cd SLHCUpgradeSimulations/Geometry/test  

b) Edit the file writeFile_phase2BE5D_cfg.py and change the line :  

    process.load('Configuration.Geometry.GeometryExtendedPhase2TkBE5D_cff')  
by :  
 
    process.load('Configuration.Geometry.GeometryExtended2023Muon_cff')  
  
save and do :  

    cmsRun writeFile_phase2BE5D_cfg.py  
(it will take ~1 minute)  
  
c) Once done , do ls -trl , you will find the txt file which does contain the list of detids of your geometry :  

    PixelSkimmedGeometry_phase2BE5D.txt  
  
without any xml change, this file should be strictly the same as :  

    CMSSW_6_2_0_SLHC25/src/SLHCUpgradeSimulations/Geometry/data/PhaseII/Pixel10D/PixelSkimmedGeometry.txt  
  
If you notice a diff, then replace it with the new one :  

    cp PixelSkimmedGeometry_phase2BE5D.txt   ../data/PhaseII/Pixel10D/PixelSkimmedGeometry.txt  
  
and that's it.  
  
**5) Example of run**
    See above

```
cd CMSSW_11_1_5/src
runTheMatrix.py -l 27434.0 --command "-n 1"
```
