# GEM_Efficiency
## Analysis Related
- VFAT Mask investigate issue reported [here](https://indico.cern.ch/event/1219126/contributions/5128191/attachments/2542791/4378236/FIvone_OpMeeting7Nov2022.pdf#page=12)
- Efficiency vs eta partition: investigate issue reported [here](https://indico.cern.ch/event/1213272/contributions/5150460/attachments/2556528/4406482/FIvone_32ndGEMWorkshop_GE11Efficiency.pdf#page=14)
- Complete analysis on 2022 data
- Checks on GE21 code [here](https://github.com/fraivone/PFA_Analyzer/tree/feature/GE21Only_and_python3)
  -  Run on MC
  - Run on 2022 data
- High occupancy events we know what triggers these events. Looking for the best way to mask them out offline.

## NTuplizer related (more technical)
- CMS Reconstructed muon tracks (Stand Alone Muos STA) do make use of GEM Rechits. It biases the evaluation of the spatial resolution. Possible solutions:
  1. Propagated the ME11 segments
  2. Remove the GE11 hits and re-fit the tracks
- VFAT Mask: at the moment the VFAT mask are not stored in the RECO data. Issue reported [here](https://indico.cern.ch/event/1213272/contributions/5150460/attachments/2556528/4406482/FIvone_32ndGEMWorkshop_GE11Efficiency.pdf#page=6). To be further discussed with Marcello

As we discussed today, the efficiency analysis is split in 2 steps:

1. Ntuplization
2. Analysis of the ntuples

## 1. Ntuplization
CMSSW code meant to skim the data and perform the propagation of muon tracks on GE11 surface

[github repo](https://github.com/gem-dpg-pfa/MuonDPGNTuples/tree/GEMOHStatusCMSSW_12_6_X)

## Download and Compile
on your lxplus

```
cmsrel CMSSW_12_5_1
cd CMSSW_12_5_1/src/ 
cmsenv
git cms-merge-topic yeckang:OHStatus_12_5_X
git clone --branch GEMOHStatusCMSSW_12_6_X git@github.com:gem-dpg-pfa/MuonDPGNTuples.git MuDPGAnalysis/MuonDPGNtuples
cd MuDPGAnalysis/MuonDPGNtuples/
scram b -j 5
```
## Run the ntuplization
The code can be executed 

locally in your lxplus area
1. distributed at the CMS computing centers
2. I recommend running it locally first.

### Run locally
The output file will be stored under the `test/folder`. 
The input parameters are provided in the file `test/muDpgNtuples_cfg.py`
Among the many parameters of primary importance are:

1. the globalTag [link](https://github.com/gem-dpg-pfa/MuonDPGNTuples/blob/21a2aa3921046fedcb1ed6943beb97960e471f5e/test/muDpgNtuples_cfg.py#L12) depends on the CMS configuration at the time of the run
2. the input file [link](https://github.com/gem-dpg-pfa/MuonDPGNTuples/blob/21a2aa3921046fedcb1ed6943beb97960e471f5e/test/muDpgNtuples_cfg.py#L93) which specifies what files are you running on. You might need a working voms authentication to access the input files which are located in sites other than CERN.
You don't need to edit the config file, simply run
```
cmsenv
cmsRun  muDpgNtuples_cfg.py
```
to start the ntuplization. 

## Run with CRAB (CMS computing centers)
The output will be stored under `/eos/cms/store/group/dpg_gem/comm_gem/P5_Commissioning/2022/GEMCommonNtuples`
Therefore writing rights are needed for the GEM DPG EOS (EOS T2 facility)
Subscribe to the e-group `cms-eos-dpg-gem` to have writing rights.
You must have a working voms authentication to access the input files which are located in sites other than CERN.

```
cd CRAB_SUB/
cmsenv
source "/cvmfs/cms.cern.ch/common/crab-setup.sh"
python3  P5Data_crabConfig.py  --RunList [space separated list of runs to be ntuplized]--Dataset [chosen dataset among the available options
```
This command submits the jobs on CRAB. You can check the status of your jobs by running
```
python3 CheckStatus.py
```

## 2. Analysis of the ntuples
This code is meant to analyze the ntuples and produce efficiency info per VFAT.
It actually produces many plots of many quantities. 

This code can largely benefit from `columnar analysis` which is what I am working on at the moment. 
The code currently on github processes the events one by one in a for loop. It typically takes ~hours to process a run.
With the `columnar analysis` it will be 1-2 order of magnitude faster. 

[github repo](https://github.com/fraivone/PFA_Analyzer/tree/main)

The [README](https://github.com/fraivone/PFA_Analyzer/tree/main#readme) details the usage and functioning of the code.
I recommend reading it first. You should be able to try it out following the instruction in the there.
I recommend to checkout the branch `main`.

My latest working branch is `feature_python3`:

- based on python3 (perviously python2.7)
- includes changes in the the input parameters parsing
- includes VFAT mask event by event
- manages the dependencies through poetry
The core of the code stays the same, but the instructions for this version have not been written :confounded:.

## Masking chamber based on the HV
Additional code that helps you select only the **lumisections** for which **GE11 HV == your desired value**

This is a stub: I intend to write this section, but haven’t yet.

## Next steps

- Have the "FetchOMS" script work
  1. Produce keys and cert
  2. Link the key and cert location on the
  3. run following 
     ``` 
     cd /venv/bin 
     source activate
     ```
  4. Start fetching info from run 357899 to run 363000
  5. Filter based on duration (>1h) and date (up to 27 Nov)
- Use the latest version of the analyzer
  1. Install poetry [link](https://python-poetry.org/docs/)
    a. Before installing Poetry enable the python3 version higher than python3.7. Instructions are at [here](https://cern.service-now.com/service-portal?id=kb_article&sys_id=3554cdc50a0a8c0800e89d3ccb5ed4a7) for the lxplus
  3. git clone --branch feature_python3 https://github.com/fraivone/PFA_Analyzer.git

- TO DO Francesco 

  1. Provide the chamber masking script
  2. Check with Simone the status of the code after HV Extension

### On the VFAT Mask
Two cases:

1. OHStatus available in the data:
This is the optimal case for masking. The information on VFAT Errors, Warnings, ZeroSuppressed, VFAT Masked at the FW/DAQ level are stored per event.
I fetch the information per VFAT per event and store them into the ntuples. 
At the analysis level, for each event, I skip the propagated hits which go on a "BAD" VFAT

2. OHStatus NOT available in the data:
I rely on the DQM Summary plot. Chambers that appear "not green" in the DQM Summary plot are masked entirely for the whole run
