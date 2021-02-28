
# Falcon
Using machine learning to build a fast detector simulator that maps parton jets to reco-jets. This project uses data generated [here](https://github.com/jblue1/EventGeneration/).

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.4569082.svg)](https://doi.org/10.5281/zenodo.4569082)

## Requirements
 - [Root](https://root.cern/install/) (tested on version 6.22.2)
 - [FastJet](http://fastjet.fr/) (tested on version 3.3.4)
 
 Other requirments listed in requirements.txt.

Using the makefile requires the following environmental variables to be set
(if you ran ```source bin/thisroot.sh``` in your root installation or
installed root in an active conda environment, ROOTSYS has already been set).
```Makefile
export ROOTSYS=path/to/your/root/installation 
export FASTJETSYS=path/to/your/fastJet/installation
```

## Use
In addition to the existing directories, add the following empty directories
```
falcon
│
└───data
|   └───plots
│   └───processed
│   └───raw
└───bin
```

### Analyzing data
There are two programs that analyze data. Make sure the output root file from [here](https://github.com/jblue1/EventGeneration/) is located in ```/falcon/data/raw```, and that the name of this file is ```events.root```. To match the parton, reco and gen jets in the root file, run ```make makeHistos.out``` and then ```make histos```. A root file containing various histograms relating to the matching will be in ```/falcon/data/plots/histos.root```. To write out the 4-momenta of matched parton and reco jets to be used later for machine learning, run ```make writeJetMomenta.out``` and then ```make data```, which will write the 4-momenta of the matched jets to ```/falcon/data/processed/matchedJets.txt```.

### Training
There are currently three machine learning models that have been used for this project. A fully connected network, a conditional GAN, and a conditional Wasserstein GAN. To train a model, move to the ```/falcon/src/learning/``` directory. In the directory there is a ```train.py``` script, as well as a ```example.json``` file in which training/model hyperparameters are set. The ```train.py``` takes two arguments, a choice of model (either FCNN, cGAN, or cWGAN), and the name of the configuration file. To train the cWGAN, for example, run ```python train.py cWGAN example.json```. This will create the directry ```/falcon/models/cWGAN/Run_<todays date>_0/``` where the losses and weights will be saved. 

### Putting it all together
After cloning the repo, importing the root file produced from [here](https://github.com/jblue1/EventGeneration/) and creating the directories as described above, running the code below would cluster the parton jets, match parton and reco jets, and then train a conditional GAN to generate realistic reco jet 4-momenta given a parton jet 4-momentum.
```bash
make writeJetMomenta.out
make data
cd src/learning/
python train.py cWGAN example.json
```

