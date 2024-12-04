# AI rewritten GENIE README

## Table of Contents
1. [Model Overview](#model-overview)
2. [Training the Model](#training-the-model)
3. [Processing Continuous Data](#processing-continuous-data)
4. [Input Data Format](#input-data-format)
5. [Output Format](#output-format)

## Model Overview

The GENIE model processes seismic data through a series of scripts defined in the "Applying the model" section. Configuration is primarily managed through three YAML files:
- `config.yaml`
- `train_config.yaml`
- `process_config.yaml`

## Training the Model

### Using train_GENIE_model.py

#### Configuration Parameters
The training parameters in `train_config.yaml` should be adjusted based on your specific use case. Key parameters are grouped into three lists:
- `training_params`
- `training_params_2`
- `training_params_3`

These parameters control:
- Average background rate of sources
- Missed and false pick rates
- Travel time uncertainty levels
- Source and spatial label kernel widths
- Maximum moveout distances of sources

#### Performance Optimization
- **Training Data Management**:
  - Set `build_training_data = True` to pre-build and save training data
  - Warning: Saved files can reach ~TB in size
  - Default behavior builds new batches between update steps

#### Special Considerations
- Use `fixed_subnetworks` parameter to train GNN on specific subnetwork instances
- Scale-dependent parameters in `module.py`:
  - `scale_rel`
  - `scale_t`
  - `eps`
  - These normalize node offset distances and arrival time uncertainties
  - Should be decreased for small applications (< 50 km)
  - Future versions will move these to config files

## Processing Continuous Data

### Setup and Execution
1. Create a process days list file: `{Project_name}_process_days_list_ver_1.txt`
2. Format: One date per line (YYYY/MM/DD)
3. Run command: `python process_continuous_days.py {index}`
   - `index` refers to the line number in the days list (0-based)

### Output Structure
- Location: `Catalog` folder
- File format: HDF5
- Naming: `Project_results_continuous_days_YYYY_M_D_ver_1.hdf5`

#### Source Locations
Two versions are saved:
1. `srcs`: Direct model predictions
   - More consistent
   - May retain some bias
   - Shows clustering due to source graph node positions

2. `srcs_trv`: Travel-time based optimized locations
   - Generally more accurate
   - More susceptible to misassociation errors

*Note: Additional refinement possible using NonLinLoc or HypoDD*

## Input Data Format

### Pick File Requirements
- Location: `{path_file}/Picks/{YYYY}_{MM}_{DD}_ver_{n_ver}.npz`
- Required Fields:
  1. `P` (Pick data matrix)
  2. `sta_names_use`
  3. `sta_ind_use`

#### Pick Data Matrix Columns
1. Time since start of day
   - Can be absolute time or sampling rate-based
   - Configure via `spr_picks` in `process_config.yaml`
2. Station index
3. Maximum peak ground velocity (±1s to +2.5s window)
4. PhaseNet pick probability
5. Phase type (0 = P waves, 1 = S waves)

#### Station Information
- `sta_names_use`: Active stations for the day
- `sta_ind_use`: Station indices corresponding to names

### Example Data
Sample pick files available at:
`https://github.com/imcbrearty/GENIE/tree/main/BSSA/Datasets/500%20random%20day%20test`

## Future Improvements
1. Speed optimization for input feature creation
2. Streamlined post-processing for:
   - Discrete associations
   - Source number determination
3. Enhanced documentation
4. Configuration file integration for hard-coded features