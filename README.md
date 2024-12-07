# PLAN Project README

This is the GENIE repo for the PLAN project for the study of the aftershocks of the Kahramanmaraş earthquake.

We're using the old GENIE codebase, but with some modifications to the config file and data input to suit our needs.

In addition, the station location ranges have been calculated using the stations that we have in our dataset.

# Setup Parameters

To setup the model, we've adjusted the config.yaml to use the "KOERI" client with the "KO" network, adjusted latitude/longitude filters and the time range for our purposes.

```diff
diff --git a/Code/config.yaml b/Code/config.yaml
index eca6639..1383685 100644
--- a/Code/config.yaml
+++ b/Code/config.yaml
-latitude_range: [18.8, 20.3] # Latitude range of the region that will be processed
-longitude_range: [-156.1, -154.7] # Longitude range of the region that will be processed
+latitude_range: [39.0, 41.0] # Latitude range of the region that will be processed (Turkey central region)
+longitude_range: [32.0, 35.0] # Longitude range of the region that will be processed (Turkey central region)

  time_range: # This sets up the Catalog and Pick files to have these years initialized
   start: '2018-01-01'
-  end: '2023-01-01'
+  end: '2023-12-01'

-client : 'IRIS'
-network: 'HV'
+client : 'KOERI'
+network: 'KO'
```

To ensure accurate velocity modeling, we've used the 1D velocity model of the Turkish Peninsula, which we take from [this paper's](https://www.researchgate.net/publication/358489567_Active_Seismotectonics_of_the_East_Anatolian_Fault) supplement

# GENIE : Graph Earthquake Neural Interpretation Engine README

A Graph Neural Network (GNN) based earthquake phase associator and spatio-temporal source localization model.

The paper associated with this work is given at https://pubs.geoscienceworld.org/ssa/bssa/article/doi/10.1785/0120220182/619845/Earthquake-Phase-Association-with-Graph-Neural.

The source code is posted at https://github.com/imcbrearty/GENIE/tree/main/Code.

Note that this is an early release of the code. Increased documentation, more thorough user testing, and pre-trained models will be forthcoming.

## Applying the model

For now, the basic workflow is to (i). install dependencies in "install_dependencies.txt", (ii). run the "make_initial_files.py" script to initialize station, region, and velocity model files, (iii). run "assemble_network_data.py" for an input set of stations and spatial region, which sets up the directory and initilizes required variables, (iv). run "calculate_travel_times.py" to compute the travel time grid of P and S phases over the region of interest, for a chosen velocity model, (v). run "train_GENIE_model.py" to train the GNN for the given application, and (vi). run "process_continuous_days.py" to compute predictions and build an earthquake catalog for a given set of input picks, and the current trained GNN and velocity model.

The config files can be used to adjust most parameters. Also, station, region, and 1D velocity model files can be created following the same format as shown in make_initial_files.py without actually having to run make_initial_files.py.

Pre-trained GNN's, and pre-computed travel time fields will be supplied in the future, to faciliate easier use and allow users to only have to run steps (i-iii), and (vi). above. Running additional re-location techniques like NonLinLoc or HypoDD with the associated picks from this model can often improve event location accuracies.


## Extra information

The pre-print is given at https://arxiv.org/abs/2209.07086. The datasets used in this study are available in https://github.com/imcbrearty/GENIE/tree/main/BSSA.

For a description of a related GNN architecture applied to source localization from discrete pick datasets, see https://ieeexplore.ieee.org/document/9897468, https://arxiv.org/abs/2203.05144.

Preliminary results of the method are shown here, https://www.scec.org/meetings/2021/am/poster/227, https://www.scec.org/meetings/2023/am/poster/013
