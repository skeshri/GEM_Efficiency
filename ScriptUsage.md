```
usage: PFA_Analyzer.py [-h] [-pc PHI_CUT] [-rdpc RDPHI_CUT]
                       [--chi2cut CHI2CUT] [--minPt MINPT]
                       [--chamberOFF [CHAMBEROFF [CHAMBEROFF ...]]]
                       [--VFATOFF [VFATOFF [VFATOFF ...]]]
                       [--outputname OUTPUTNAME] [--fiducialR FIDUCIALR]
                       [--fiducialPhi FIDUCIALPHI] [--maxErrPropR MAXERRPROPR]
                       [--maxErrPropPhi MAXERRPROPPHI] [--DLE] [--FD]
                       [--verbose] --dataset [DATASET [DATASET ...]]
                       [--minME1 MINME1] [--minME2 MINME2] [--minME3 MINME3]
                       [--minME4 MINME4] [-evts NEVENTS]
```
Scripts that: 
	- Reads the GEMMuonNtuple
	- Plot Sanity Checks
	- Plot Residuals (takes the cut as parameter)
	- Plot efficiency

Currently allows the hits matching on glb_phi and glb_rdphi



optional arguments:

  -h, --help            show this help message and exit

  -pc PHI_CUT, --phi_cut PHI_CUT

                        Maximum allowed dphi between RecoHit and PropHit to be counted as matched hit

  -rdpc RDPHI_CUT, --rdphi_cut RDPHI_CUT

                        Maximum allowed rdphi between RecoHit and PropHit to be counted as matched hit

  --chi2cut CHI2CUT     Maximum normalized chi2 for which accept propagated tracks

  --minPt MINPT         Minimum pt for which accept propagated tracks

  --chamberOFF [CHAMBEROFF [CHAMBEROFF ...]]

                        file_path to the file containing a list of the chambers you want to exclude for the run (i.e. GE11-M-29L2)

  --VFATOFF [VFATOFF [VFATOFF ...]]

                        file_path to the file containing a list of the VFAT you want to exclude from efficiency evaluation. The file must be tab separated with region,layer,chamber,VFAT,reason_mask

  --outputname OUTPUTNAME

                        output file name

  --fiducialR FIDUCIALR, -fR FIDUCIALR

                        fiducial cut along R axis

  --fiducialPhi FIDUCIALPHI, -fP FIDUCIALPHI

                        fiducial cut along phi axis

  --maxErrPropR MAXERRPROPR

                        max error on propagated R in order to accept the muon (cm)

  --maxErrPropPhi MAXERRPROPPHI

                        max error on propagated phi in order to accept the muon (rad)

  --DLE                 Swtiches on the Double Layer Efficiency (DLE) analisys. False by default

  --FD                  When enabled, allows the storage of all GEM RecHit Digis. False by default, which means that GEM RecHit Digis are stored only for EVTs in which STA propagation hits GEM

  --verbose             Verbose printing

  --dataset [DATASET [DATASET ...]], -ds [DATASET [DATASET ...]]

                        TAG to the folder containing the NTuples to be analyzed

  --minME1 MINME1       Min number of ME1 hits

  --minME2 MINME2       Min number of ME2 hits

  --minME3 MINME3       Min number of ME3 hits

  --minME4 MINME4       Min number of ME4 hits

  -evts NEVENTS, --nevents NEVENTS

                        Maximum allowed dphi between RecoHit and PropHit to be counted as matched hit



Typical exectuion

	 python PFA_Analyzer.py  --phi_cut 0.001 --rdphi_cut 0.15
