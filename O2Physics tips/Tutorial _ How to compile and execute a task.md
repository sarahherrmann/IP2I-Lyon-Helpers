# Tutorial : How to compile and execute a task

## I. First step : Write the task

The required parts are the following: 
 - The task must be a .cxx file
 - The .cxx file must contain the lines :
```C
#include "Framework/runDataProcessing.h"
#include "Framework/AnalysisTask.h"
```
- The .cxx macro must have a **struct** inside of it
- Must have a `WorkflowSpec defineDataProcessing(ConfigContext const& cfgc)`

Example of a minimal task myTask: 

```C
#include "Framework/runDataProcessing.h"
#include "Framework/AnalysisTask.h"


struct myTask
{
    void process(...)
    {
        ...
    }
    
};


WorkflowSpec defineDataProcessing(ConfigContext const& cfgc)
{
  return WorkflowSpec{
    adaptAnalysisTask<myTask>(cfgc),
  };
}
```

In the `WorkflowSpec` part you have do add the line `adaptAnalysisTask<myTask>(cfgc)` for every task written in your .cxx file

For example if you have 

```C
struct myTaskA
{
    void process(...)
    {
        ...
    }
    
};

struct myTaskB
{
    void process(...)
    {
        ...
    }
    
};
```
You have to write 
```C
WorkflowSpec defineDataProcessing(ConfigContext const& cfgc)
{
  return WorkflowSpec{
    adaptAnalysisTask<myTaskA>(cfgc),
    adaptAnalysisTask<myTaskB>(cfgc)
  };
}
```

::: info
NB : If you want to use a `HistogramRegistry`you have to declare it inside your task 
(inside the **struct**)
:::

### **Where should I put my piece of code my-task.cxx ?**
The convention is to put it in `O2Physics/PWGXX/Tasks`
with `XX` the initials of the most adapted PWG for the purpose of your task, for example PGWMM if you are writing a multiplicity analysis.


## II. Second step : Compile the task

_________________________________

- add the thingy in the cmake list (attention au nom de la task)
-  ninja 
-  commit -m pour etre vu par alibuild (peut etre aussi par noinja) 
-  homebrew install ninja
-  mettre la tache dans PWGXX/Tasks
_______________________
Once your analysis task is written and is placed in the right directory, you have a few things to do to ensure that O2Physics recognises your task and is able to execute it later on. 

1. Add a few lines of code to the file CMakeLists.txt, the one in the directory `O2Physics/PWGXX/Tasks` :

```
o2physics_add_dpl_workflow(my-task
                    SOURCES my-task.cxx my-task.h
                    PUBLIC_LINK_LIBRARIES O2::Framework O2Physics::AnalysisCore
                    COMPONENT_NAME Analysis)
```

:::info
NB : The name of your task (here my-task) can contain only lowercase letters 
and **-** symbols
:::

If you have a header file associated to your code `my-task.cxx` you also have to add it in the `SOURCES`, just like the above example. If you don't have one, it's fine too.


If you need to load a library that is outside O2Physics, you also have to specify it in the CMakeLists.txt, example with the library fastjet: 

```
if(FastJet_FOUND)
o2physics_add_library(PWGJECore
               SOURCES  JetFinder.cxx
               PUBLIC_LINK_LIBRARIES O2Physics::AnalysisCore FastJet::FastJet FastJet::Contrib)

endif()
```

2. Commit your changes by doing :
`git add my-task.cxx` 
`git commit -m "some message here"` 
By doing that if you rebuild O2Physics (with aliBuild), your task can be visible for aliBuild.

3. Either rebuild O2Physics for your task to be compiled, or use ninja under the O2Physics environment to compile only your task or all the tasks in a particular `PWGXX` directory 
(to use ninja to do that see https://aliceo2group.github.io/analysis-framework/docs/installing/#building-partially-for-development-using-ninja)

![](https://codimd.web.cern.ch/uploads/upload_944213c60c04c7f3ccc1577fea418e02.png)

## Building partially for development using ninja

You need first to **install ninja**
On ubuntu, procedure described [here](https://lindevs.com/install-ninja-build-system-on-ubuntu)

This requires that the O2Physics build succeeded. Enter the environment as explained in the previous step specifying in addition the ninja package:

```bash
alienv enter O2Physics/latest ninja/latest
```

Go to the build directory
```bash
cd ~/alice/sw/BUILD/O2Physics-latest/O2Physics
```

You can now rebuild a specific directory with

```bash
ninja install <your-analysis-directory>/all
```
For example:
```bash
ninja install PWGCF/Tasks/all
```

A specific executable can be build with
```bash
ninja install O2Physicsexe-<target>
```
For example (note the missing o2-):
```bash
ninja install O2Physicsexe-analysis-cf-correlations
```

:::danger
This only builds O2Physics. If you have updated O2 or alidist, you first need to do a full build with aliBuild. Then enter the environment again before using ninja.
:::




## III. Run your task

Once your task has been sucessfully compiled, it should be visible by O2Physics, to check if it is do (xx still being the initials of the appropriate PWG)

```
alienv enter O2Physics/latest
o2-analysis-xx-my-ta
```
and see if the computer can autocomplete it into 
```o2-analysis-xx-my-task ```
If it does, you are good to go !

You can then go to a specific directory inside of which you have an AO2D.root file, and run for example (under the O2Physics environment) : 
```o2-analysis-mm-my-task --aod-file AO2D.root```


## IV. Other pieces of information

### `DEBUG` flag
It is recommended to build O2 and O2Physics with the `debug`flag to have errors which are more understandable, to do so the synthax is: 

``aliBuild build O2 --defaults o2 --debug``

### Stored tables

The tables with `Stored` names like `StoredMFTTracks` are not made to subscribe to them, thus is leads to a compilation error when you try to do :
```C
void process(o2::aod::StoredMFTTracks const& tracks)
{
    ...
}
```

You have to subscribe to the extended version of these tables, for example `MFTTracks`



All the tables in AO2D files are described here : https://aliceo2group.github.io/analysis-framework/docs/datamodel/ao2dTables.html


### On extended tables : 

`MFTTracks` is an extended table, that is made from stored MFT tracks and calculated Pt, eta and P, which is done by automatic aod-spawner task. 


If you have a crash during this aod-spawner task it is then possible that something happens during the calculation, perhaps because there is a floating point error (if 1/pt is too small or tgl is incorrect, or any other problem with the incoming data)

The description of this aod-spawner task is in https://github.com/AliceO2Group/AliceO2/blob/dev/Framework/Core/include/Framework/AnalysisDataModel.h#L373

As you can see for example in line L373,
```C
DECLARE_SOA_EXPRESSION_COLUMN(Pt, pt, float, //!
                              nabs(1.f / aod::fwdtrack::signed1Pt));
```

the column `Pt` is filled with a float named `pt`calculated with 
pt = 1/ $\lvert$ `aod::fwdtrack::signed1Pt` $\rvert$

If `aod::fwdtrack::signed1Pt`=0 with then perform a division by 0 and it leads to a crash, for instance.


:::info
You cannot put several process functions in the same task, if you do you will have the following message : 
```
[WARN] Task analyse-m-f-t-tracks has no inputs
[ERROR] error parsing options of o2-analysis-mm-analyse-mft-tracks: 
unrecognised option '--aod-file'
```
:::

Each process function, that is not named process() needs to have an accompanying PROCESS_SWITCH() declaration. See, for example, `multiProcess.cxx` in tutorials.

```cpp=
PROCESS_SWITCH(NameOfTheTask, processName, "Process name description", false);
```
if the last argument is `false`, the processName function will not run, if it is true, it will run.

It is possible to have more than one process function with the `true` argument.

## Direct declaration of an histogram Registry

```cpp=
HistogramRegistry registry
  {
     "registry",
     {
       {"histoName", "Title; Xtitle; Ytitle", {HistType::kTH1F, {{nBinX, Xinf, Xsup}}}, true}         //
     }                                                                                                               
   };
```

The `true` indicates that the method `Sumw2()` must be applied to "histoName"



## DEBUGGING
### SEG FAULT

**Number one reason of seg fault**

When you ask for example track.collision() but you have subsribed to the table `soa::Join<aod::Collisions, aod::EvSels>`
You have to ask for `track.collision_as<soa::Join<aod::Collisions, aod::EvSels>>();`

**Other possible reasons**

LOGF is not made to print numerous messages, if the task will plot several LOGF messages one after the other, it might give a SEG FAULT

example: 
LOGF(debug, "Table id %d BC %llu", bcLast.globalIndex(), bcLast.globalBC()); creates yet another seg fault

### 1. Missing trees
If you get this error:
:::danger
[ERROR] Exception caught: Couldn't get TTree "DF_2850640012345678000/O2mcparticle_001" from "AO2D.root". Please check https://aliceo2group.github.io/analysis-framework/docs/troubleshooting/treenotfound.html for more information. 
:::

Then it means you need to add `| o2-analysis-mc-converter --configuration=json://conf2-sim.json` at the end of your o2 command line

Missing trees could also mean you need to add dependencies, or the configuration in your json file is wrong (for example event selection of run2 so it looks for VOA)

Or there was a change in the AnalysisDataFormat 
### 2. Error before entering the process

If you have an error of this type : 
```
[49904:vertexingfwd]:     1   libsystem_platform.dylib            0x00007fff203e3d7d: _sigtramp()
[49904:vertexingfwd]:     2   ???                                 0x00007ffd73b38d30: 0x0()
```
before even entering the process function, you should check that you subsribed to the right tables, for example if you subscribe to FullBCs where FullBCs is defined by :

`using FullBCs = soa::Join<aod::BCs, aod::MatchedBCCollisionsSparse>;`

Then if you ask in you task something like 
`collision.bc()` or `ambitracks.bc()`, it will not work because you subscribed to a more complicated table than just the BC table, and it will crash without entering the process (for some reason).

### 3. Some unexplicable error about filtering

You can not filter a table that has already been filtered, for example if you use 
```
expressions::Filter ITStracks = (aod::track::detectorMap & (uint8_t)o2::aod::track::ITS) != (uint8_t)0;

  using ExTracks = soa::Join<aod::Tracks, aod::TracksExtra, aod::TracksDCA>;
  using FiTracks = soa::Filtered<ExTracks>;
```

Then you must not subscribe to soa::Filtered<FiTracks>
    
### 4. Error linked to JAlien
    
```
[97536:track-propagation]: Error in <TJAlienConnectionManager>: Context creation failure
[97536:track-propagation]: Info in <TJAlienConnectionManager>: Opening connection to 137.138.99.141
[97536:track-propagation]: Error in <TJAlienConnectionManager>: Context creation failure
[97536:track-propagation]: Info in <TJAlienConnectionManager>: Opening connection to 137.138.99.139
[97536:track-propagation]: Error in <TJAlienConnectionManager>: Context creation failure
[97536:track-propagation]: Info in <TJAlienConnectionManager>: Opening connection to 137.138.47.221
[97536:track-propagation]: Error in <TJAlienConnectionManager>: Context creation failure
[97536:track-propagation]: Info in <TJAlienConnectionManager>: Opening connection to 137.138.47.222
[97536:track-propagation]: Error in <TJAlienConnectionManager>: Context creation failure
[97536:track-propagation]: Info in <TJAlienConnectionManager>: Opening connection to 137.138.47.216
```


Solution : type alien.py and enter your password
    
### 5. Error about template

```
error: use 'template' keyword to treat 'mfttrack_as' as a dependent template name
      auto track = ambitrack.mfttrack_as<MFTTracksLabeled>(); // Obtain the MFT ambiguous track with the MC labels
                             ^
                             template 
```
    
Because ambitrack is a templated type, and mfttrack_as is a templated function, you have to write :
    `ambitrack.template mfttrack_as<MFTTracksLabeled>();`
## json files

Say smthg about json files and always put the command with the json file, even if there is nothing about it inside (I guess)

`o2-analysis-timestamp --configuration=json://conf3-sim.json | o2-analysis-event-selection --configuration=json://conf3-sim.json | o2-analysis-trackextension --configuration=json://conf3-sim.json | o2-analysis-mm-dndeta-mft --configuration=json://conf3-sim.json | o2-analysis-mc-converter --configuration=json://conf3-sim.json`

## The use of json files for configuration

json files can be used to specify what needs to be written (see section), but they can also be used to give some information about the configuration to the workflow. For example you can put the values of the configurables inside a json file. 

### Example of json file

```json=
{
    "internal-dpl-clock": "",
    "internal-dpl-aod-reader": {
        "time-limit": "0",
        "orbit-offset-enumeration": "0",
        "orbit-multiplier-enumeration": "0",
        "start-value-enumeration": "0",
        "end-value-enumeration": "-1",
        "step-value-enumeration": "1",
        "aod-file": "@inputs-sim.txt"
    },
    "internal-dpl-aod-spawner": "",
    "internal-dpl-aod-index-builder": "",
    "timestamp-task": {
        "verbose": "false",
        "rct-path": "RCT\/RunInformation",
        "start-orbit-path": "GRP\/StartOrbit",
        "ccdb-url": "http:\/\/alice-ccdb.cern.ch",
        "isRun2MC": "false"
    },
    "bc-selection-task": {
        "processRun2": "false",
        "processRun3": "true"
    },
    "track-extension": {
        "processRun2": "false",
        "processRun3": "true"
    },
    "event-selection-task": {
        "syst": "pp",
        "muonSelection": "0",
        "customDeltaBC": "300",
        "isMC": "true",
        "processRun2": "false",
        "processRun3": "true"
    },
    "pseudorapidity-density": {
        "estimatorEta": "1",
        "useEvSel": "true",
        "useDCA": "true",
        "maxDCAXY": "2.4",
        "maxDCAZ": "3.2",
        "processTagging": "true",
        "processGen": "true"
    },
    "internal-dpl-injected-dummy-sink": "",
    "internal-dpl-aod-global-analysis-file-sink": ""
}


```

### Quick explanation 

In the above example of json file, in the `pseudorapidity-density` section (line 37 to 44), you can see the different values of the configurables, `maxDCAZ` is now 3.2 for instance. You can also specify the type of data you are processing with the flags `processRun2` `processRun3` and `isMC`. 

Another very useful thing is that inside this file you can provide a list of aod files to analyse, as you can see the line 10 `"aod-file": "@inputs-sim.txt"` means that the workflow will analyse the all the files contained in the txt file `inputs-sim.txt`, with one file name per line. 

### How to give this json file to the workflow

You have to provide the json file to the workflows with the command `--configuration=json://config-file.json`

For example the above json file is well adapted for the task `o2-analysis-mm-dndeta`, you could run this workflow by typing this command inside the O2Physics environment : 

`o2-analysis-timestamp --configuration=json://config-file.json | o2-analysis-event-selection --configuration=json://config-file.json | o2-analysis-trackextension --configuration=json://config-file.json | o2-analysis-mm-dndeta --configuration=json://config-file.json`

N.B. : You should provide the json file to each workflow separated by a pipe. 


## Configurables
### ConfigurableAxis

In order to have axis binning and limits configurable, one should use the object `ConfigurabeAxis`. Let's say I have an histogram called `histoConfig` and I want its axis to be configurable.
    
    
For it to work one must strictly respect the following sythax:

- In the struct declare the ConfigurableAxis (and do not declare the AxisSpec)
- Declare the HistogramRegistry but do not put `histoConfig` in it.
- Initialise the AxisSpec and then the `histoConfig` in the <font color="blue">HistogramRegistry</font>
    
Code example: 
```Cpp
struct MyStruct 
{
    ConfigurableAxis multBinning{"multBinning", {301, -0.5, 300.5}, ""};
    
    HistogramRegistry registry{
    "registry",
    {{"TracksPhi", "; #eta;", {HistType::kTH1F, {{600, 0, 2 * M_PI}}}}}                               
    };
    
    void init(InitContext&)
    {
    AxisSpec MultAxis = {multBinning, "N_{trk}"};
    registry.add({"histoConfig", "", {HistType::kTH1F, {MultAxis}}}) ;
    }
    
    
};
```

There is an example in [tutorial/histogramRegistry.cxx](https://github.com/AliceO2Group/O2Physics/blob/master/Tutorials/src/histogramRegistry.cxx)
    
    
## Strange things 

If you subscribe to `o2::soa::SmallGroups<soa::Join<aod::Collisions, aod::McCollisionLabels>> const& collisions` it works, but not if you subscribe to `soa::Join<aod::Collisions, aod::McCollisionLabels> const& collisions`, it gives an error 
```
[09:17:23][ERROR] Exception caught: 
Table Collisions index fIndexMcCollisions is not sorted: 

next value 23 < previous value 24!
```

if you try to process this: 

`soa::Join<o2::aod::MFTTracks, o2::aod::McMFTTrackLabel> const& tracks`

it will give you the strangest error, but if you try this instead : 


  `void processGen(soa::Join<o2::aod::MFTTracks, o2::aod::McMFTTrackLabels> const& tracks)`

it will work because in the first thing YOU FORGOT AN S MORON
So if something strange with ArrowHelper or smthg, you know what is happening
=> check the ==s==



If you only have one `process`function, it is very important that you remove any PROCESSSWITCH, 
if you don't the `process`function will run 2 times instead of one.

If you have errors with : `libsystem_platform.dylib`, it is probably a simple  C++ error (pointer to a strange memory region, implement security)

In a configuration.json file, don't put an empty value for a configurable to get the default value, it doesn't work and it creates multiple problems

## Anton's knowledge

The index should be directly accessible like this: auto track = ambitrack.mfttrack_as<soa::Join<o2::aod::MFTTracks, aod::McMFTTrackLabels>>();

One can also introduce an alias using MFTTracksLabeled = soa::Join<o2::aod::MFTTracks, aod::McMFTTrackLabels>; and use it to save some typing.

:::info
Templated functions :
- .has_something(), returns a boolean, true if yes, false if no
    - e.g. .has_collision() returns false if the aod object on which you apply the method has no collision associated to it, true if there is 

- .smthg_as<sometype>(), returns the aod object as a different type

:::

:::success
**Examples**
    
- `auto track = ambitrack.mfttrack_as<soa::Join<o2::aod::MFTTracks, aod::McMFTTrackLabels>>();`

- `auto bcambis = ambitrack.bc_as<soa::Join<aod::BCs, aod::MatchedBCCollisionsSparse>>();`
:::


$\rightarrow$ Before you call for anything, you have to check that it exists using the **has_** method
    
    
Le type de 1 thing provenant de soa::Join<aod::blabla, aod::bloublou>> const& things est toujours soa::Join<aod::blabla, aod::bloublou>>::iterator
    
    
    
---
    	
Anton Alkin
2:27 PM
Hi

2:28 PM






regarding the issues with reading the older mft table on hyperloop, you can work around it by defining temporarily the correct version (without tracktime and tracktimeres columns) in your task directly

	
Sarah Nina Edwige Herrmann
2:28 PM
How can I do that ?

Hi 

	
Anton Alkin
2:29 PM
you just copy the code from the AnalysisDataModel.h and put it in your task, inside namespace o2::aod {}

	
Sarah Nina Edwige Herrmann
2:29 PM
Ok, and it will overwrite the data model ?

	
Anton Alkin
2:30 PM
you will have to change the name of the table and add some other small stuff, and, of course, subscribe to this changed table in your task

start with copying the definition and changing the subscription, I think it should work like that already, if not, I will take a look

note that you need both the stored and extended parts of the definition

and rename both

# Others

auto slice0 = ambitrack.bcIds()[0];
auto slice1 = ambitrack.bcIds()[1];
    
    auto bcambis = ambitrack.bc_as<soa::Join<aod::BCs, aod::MatchedBCCollisionsSparse>>();

      for (auto& bcambi : bcambis) {

        if (!bcambi.has_collision()) {
          continue;
        }

        auto collision = bcambi.collision_as<soa::Join<aod::Collisions, aod::McCollisionLabels>>();


      } // BCs loop

# Where to find O2Physics source codes

The templated methods : `has_` `as_`... in ASoA.h

https://github.com/AliceO2Group/AliceO2/blob/0c7c3e048dfc2f358979998bff1c72fc4bd29d1b/Framework/Core/include/Framework/ASoA.h
    
    
    
---
Nikita Philip Tatsch
3:50 PM






Hey quick question,
I am trying to save the collision_001 table form the collsion converter alongside some other tables in a root file using the ‐‐‐‐aod-writer-json option and a file. Currently i have the following name inside the file:  

...
{
        "table": "AOD/COLLISION_001/0"
},
...
it's the only table that doesn't get saved but I need it. It's probably called differntly does anybody know how?

New Messages
	
Daiki Sekihata
4:10 PM
(message deleted)

	
Anton Alkin
4:13 PM

it should be "AOD/COLLISION/1", the second part of the string is the "description" in terms of the data model, which is the same for all versions of the table, and the version itself is encoded in the third part
    
    
# Code extracts

## 1. Making a table associating BC to multiple collisions 
    
The creation of the table was necessary before the table aod::MatchedBCCollisionsSparseMulti was officially added
    

In bestCollisionTable.h

```C
namespace indices
{
DECLARE_SOA_ARRAY_INDEX_COLUMN(Collision, collisions);
}

DECLARE_SOA_TABLE(MatchedMulti, "AOD", "MAMU",
                  indices::BCId, indices::CollisionIds);
} // namespace o2::aod
```

Even before that (all in the same code)
In trackpropagation.cxx
    
```C
namespace indices
{
DECLARE_SOA_ARRAY_INDEX_COLUMN(Collision, collisions);
}
DECLARE_SOA_TABLE(MatchedMulti, "AOD", "MAMU",
                  indices::BCId, indices::CollisionIds);
} // namespace o2::aod
struct MultiCollisionAssociation {
  Produces<aod::MatchedMulti> mm;
  struct {
    std::vector<int> colids;
  } filler;
  void process(aod::BCs::iterator const& bc, soa::SmallGroups<aod::Collisions> const& collisions)
  {
    filler.colids.clear();
    for (auto const& collision : collisions) {
      filler.colids.emplace_back(collision.globalIndex());
    }
    mm(bc.globalIndex(), filler.colids);
  }
};
```