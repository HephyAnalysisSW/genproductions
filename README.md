# genproductions
Generator fragments for MC production

The package includes the datacards used for various generators inclusing POWHEG, MG5_aMC@NLO, Sherpa, Phantom, Pythia...

Further details are reported in the twiki: https://twiki.cern.ch/twiki/bin/view/CMS/GeneratorMain#How_to_produce_gridpacks

Instructions on how to use the fragments are here https://twiki.cern.ch/twiki/bin/view/CMS/GitRepositoryForGenProduction

## Run gridpack generation
To run the gridpack generation using condor, a cms_connect account incl. registration is needed. 
To sign up to cms_connect use the guide: http://connect.uscms.org/signup

Once the registration is complete, connect to the server, change to the working directory and get a voms certificate:
```
ssh username@login.uscms.org
cd /local-scratch/username/
voms-proxy-init -voms cms -valid 192:00
```
Details can be found here: https://ci-connect.atlassian.net/wiki/spaces/CMS/pages/6783080/CMS+Connect+Quickstart

Set up the genproduction repository (no CMSSW environment needed) and change to the working directory

```
git clone https://github.com/HephyAnalysisSW/genproductions.git
cd genproductions/bin/MadGraph5_aMCatNLO/
```

Add a directory to addons/cards/CARDNAME containing a run card (CARDNAME_run_card.dat) and a process card (CARDNAME_proc_card.dat).

### Run EFT gridpack generation

Create a customize card with the EFT reference point using make_customizecard.py (example given in make_customizecard.sh). Use --append if a customize card with e.g. mass settings already exists.
```
python make_customizecard.py --filename addons/cards/CARDNAME/CARDNAME_customizecards.dat --append --referencepoint ctZ 4 ctZI 4
```
Create a reweight card with the EFT parameters (of order ORDER) and the same reference point using make_reweight_card.py (example given in make_reweight_card.sh). 
```
python make_reweight_card.py --overwrite --filename addons/cards/CARDNAME/CARDNAME_reweight_card.dat --referencepoint ctZ 4 ctZI 4 --couplings ORDER cpt 1 cpQM 1 ctZ 1 ctZI 1
```

### Submit gridpack generation
The main job will run locally and submit sub-jobs to condor. Thus, submit the job detached from the current session
(example given in submit_cmsconnect.sh).
```
nohup ./submit_cmsconnect_gridpack_generation.sh CARDNAME addons/cards/CARDNAME/ > CARDNAME.debug 2>&1 &
```

Monitor the job using
```
condor_q
```

Details here:
https://twiki.cern.ch/twiki/bin/view/CMS/QuickGuideMadGraph5aMCatNLO

