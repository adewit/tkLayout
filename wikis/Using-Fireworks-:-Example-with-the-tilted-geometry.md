Please note that general reference on Fireworks can be found at https://twiki.cern.ch/twiki/bin/view/CMSPublic/WorkBookFireworks .

The workflow is as follows:  
 
**1) Getting started**

    ssh lxplus
    cmsrel CMSSW_11_1_5
    cd CMSSW_11_1_5/src
    cmsenv
    git cms-addpkg Geometry/TrackerCommonData
    git cms-addpkg Geometry/CMSCommonData
    git cms-addpkg Fireworks/Geometry
    scram b -j 8


**2) NB : Where tilted geometry xml files are located**
This depends on where you exported the XMLs to - paths to where you copied the xmls are given in:

    src/Geometry/CMSCommonData/python/cmsExtendedGeometry2026D58XML_cfi.py

assuming that you used a D58 workflow for copying the files around

xml files & paths can of course be modified at your convenience.


**3) Run Fireworks**

Before running Fireworks, please of course edit

    src/Fireworks/Geometry/python/cmsShowFF_displayGeom_cfg.py

replacing :

    process.load("Geometry.CMSCommonData.cmsIdealGeometryXML_cfi")

by :

    process.load("Geometry.CMSCommonData.cmsExtendedGeometry2026D58XML_cfi")

NB : If you want to work on another set of xml files, please of course edit accordingly (see part 2).

Then :

**a) To visualize the geometry** :

    cmsShowFF -c $CMSSW_RELEASE_BASE/src/Fireworks/Core/macros/simGeo.fwc Fireworks/Geometry/python/cmsShowFF_displayGeom_cfg.py

**b) To check for extrusions/overlaps** :

    cmsShowFF -c $CMSSW_RELEASE_BASE/src/Fireworks/Core/macros/overlaps.fwc Fireworks/Geometry/python/cmsShowFF_displayGeom_cfg.py


Note: if you want to run fireworks remotely on a mac you'll need to run 

`defaults write org.macosforge.xquartz.X11 enable_iglx -bool true`

in a terminal and then quit xquartz, otherwise the Xwindow can't be opened.

Viewing fireworks over ssh can be a bit slow - it is faster to install a (semi-) local copy via Docker. See also: https://gitlab.cern.ch/cms-cloud/cmssw-docker . The instructions below are verified on a mac, but similar methods should be possible for other operating systems.

First install docker desktop via https://docs.docker.com/desktop/ . Allocate sufficient resources in the docker settings->resources menu (I have 4 CPUs, 5 GB of RAM and a disk image size allocation of 60GB).

Enable, in the 'experimental features' menu, the CLI experimental features (apply & restart) and in the Docker Engine menu, set 'experimental' to `true` in the config & apply+restart.

To start a docker container that runs CC7 and mounts cvmfs:

`docker run -e DISPLAY=host.docker.internal:0 --cap-add SYS_ADMIN --device /dev/fuse -it gitlab-registry.cern.ch/cms-cloud/cmssw-docker/cc7-cvmfs /bin/bash`

Note the difference wrt what's written in the gitlab repository README. `-e DISPLAY=host.docker.internal:0` ensures that an x-window can be opened to load fireworks. We're not adding `-rm` like in the gitlab README as we want to be able to stop the docker container from running without losing its contents.

Once this opens it's a good idea to add `source /cvmfs/cms.cern.ch/cmsset_default.sh` to the ~/.bashrc.

Then you can set up a CMSSW release in the usual way. On the first git-addpkg you'll have to set your git config. Remember that the vm is reading cvmfs via the network so these initial steps will be slower than on lxplus.

When you're done, you can enter another terminal on your local machine and call 
`docker ps`

Should get something like this:

```
adindas-macbook-pro:~ adinda$ docker ps
CONTAINER ID   IMAGE                                                      COMMAND                  CREATED        STATUS          PORTS     NAMES
d24d1008863f   gitlab-registry.cern.ch/cms-cloud/cmssw-docker/cc7-cvmfs   "/opt/cms/entrypoint…"   27 hours ago   Up 54 minutes             vibrant_stonebraker
```

You can stop the container with 
`docker stop [CONTAINER ID]` or `docker stop [NAME]`

And later restart it with

`docker start [CONTAINER ID]` or `docker start [NAME]`

To enter a shell in the container: `docker exec -it [CONTAINER ID] /bin/bash`


