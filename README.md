# Code and Data for Improving Bird Abundance Estimates in Harvested Forests with Retention by Limiting Detection Radius through Sound Truncation

## Description
Code and data for a live-tree retention for songbirds recovery study, including distance truncation.

## Authors
Isabelle Lebeuf-Taylor, Elly Knight, Erin Bayne

## Date
September 2024

## Experimental Methods

### Retention in regenerating harvests
This study was conducted in harvested areas of boreal and foothills forests in Alberta, Canada. We selected 392 sites, including 246 with retention patches and 146 without residual trees within 150 m. Acoustic surveys were performed using SM2, SM3, or SM4 Autonomous Recording Units (ARUs) from Wildlife Acoustics Inc., placed at least 150 m from the harvest edge. ARUs recorded 1-minute segments every 20 minutes from 1 hour before to 4 hours after dawn, for a minimum of 3 good weather days during the migratory bird breeding season (May 25th to July 6th), primarily from 2021-2023. After filtering out recordings with excessive background noise, 10 random 1-minute recordings per site were selected for analysis. Expert transcribers processed these recordings using the [WildTrax](https://wildtrax.ca) platform, identifying and tagging songs of six focal migratory songbird species. Signal strength (Sμ) for each tagged song was calculated using SoX audio processing software via [WildTrax](https://wildtrax.ca), expressed as the root mean square (RMS) of the decibel relative to full scale (dBFS), averaged between the left and right microphones.

### Sound attenuation predictive dataset
To establish the relationship between song amplitude and distance, we conducted a playback experiment using 15 indicator bird species selected to represent acoustically similar groups. These species were chosen based on a cluster analysis of song characteristics (mean peak frequency, duration, bandwidth, and spectral entropy) extracted from 3951 songs of 79 species. Playbacks were performed during the 2023 field season, using a FoxPro speaker broadcasting at 90 dB sound pressure level. At each site, songs were played at three randomly selected distances between 20 m and 150 m along a homogeneous forest transect, with the speaker positioned at an average height of 1.75 m. ARUs (SM2, SM3, or SM4) continuously recorded the playbacks. A total of 2250 songs were broadcast across various sites. The recordings were then processed in WildTrax, where each detected song was tagged and its signal strength (Sμ) was extracted, excluding recordings with excessive background noise.

## Contents
1. Data Files
2. R Scripts
3. R Markdown Document
4. Output Files
5. Software Requirements
6. Data Dictionary
7. Analysis Workflow
8. Additional Notes

## 1. Data Files
### Abundance Data
Abundance files contain counts for the Olive-sided Flycatcher (OSFL), Red-eyed Vireo (REVI), White-throated Sparrow (WTSP), Ruby-crowned Kinglet (RCKI), Yellow-rumped Warbler (YRWA), and Tennessee Warbler (TEWA). Other columns are the location and visit datatime.
- `Abundance within 150m.csv`: Counts are within 150m of ARU.
- `Abundance within 250m.csv`: Counts are within 250m of ARU.
- `Abundance untruncated distance.csv`: Counts are not distance truncated.
### Covariates.csv
- *location*: Site name
- *Year_since_logging*: years between sampling and logging event. Determined with provincial harvest polygons, and verified with Landtrendr's NBR spectral analysis.
- *latitude*: latitude of site
- *longitude*: longitude of site
- *RETN_m2*: measured area of retention patch in $m^2$
- *RETN_perimeter*: measured perimeter of patch in *m*
- *Veg_cat*: Majority vegetation composition of tree stand within 150m of sampling unit, defined as >= 70% area occupied by a tree group. Categories are Pine (jack pine or lodgepole pine), deciduous (white birch, aspen, poplar), spruce (white spruce or Engelmann spruce), mixedwood (spruce understory, deciduous overstory).
- *Dist_near_forest*: the distance form the edge of the patch to the nearest forest of a minimum 80 years of age, in *m*.

### ampl_dist_spp.csv
Contains amplitude measurements at various distances for different bird species.
- *location*: Site name
- *species_code*: 4-letter code of the species whose song was broadcast toward the recording unit
- *forest*: Forest composition type. Options are AS (aspen), BL (black spruce), MI (mixedwood), OP (open), PI (pine), SP (upland spruce).
- *distance*: distance at which song was ploayed, in *m*.
- *left_amplitude*: detected amplitude by the left mic, in *dBFS RMS*
- *right_amplitude*: detected amplitude by the right mic, in *dBFS RMS*

### Counts to truncate.csv
Long format data of mean amplitude of detected songs in harvests, including traunction model parameters. Truncation model can be applied to this data.
- *location*: site name
- *recording_date_time*: datae and time of site visit
- *species_code*: 4-letter code of detetected species. Options are TEWA, OSFL, REVI, WTSP, RCKI, YRWA
- *Year_since_logging*: years between sampling and logging event.
- *mean_amp*: amplitude avergaed between left and right mic as *dBFS RMS*.
- *SM2*: whether ARU unit was an SM2 (1) or not (0).

### locations_sm2_status.csv
Information about recording locations and SM2 recorder status.
- *location*: site name
- *SM2*: whether ARU unit was an SM2 (1) or not (0).

### number_of_transcribed_recordings_per_visit.csv
- *location*: site name
- *number_of_visits*: number of processed visits per site

### predicted_distance_amplitudes.csv
Output of playback experiment models.
- *distance*: known distance in *m* of played song
- **species_code*: 4-letter code of the species whose song was broadcast toward the recording unit
- *BinForest*: binary forest presence (FO) or absence (OP)
- *SM2*: whether ARU unit was an SM2 (1) or not (0).
- *lwr*: 2.5% of predicted amplitude in *dBFS RMS*
- *upr*: 97.5% of predicted amplitude in *dBFS RMS*
- *predicted*: predicted amplitude, given model covariate of species at specific distance in *dBFS RMS*.

### transcribed_tasks_all.csv
Recordings per site that are transcribed
- *location*: site name
- *recording_date_time*: ID of transcribed recordings

## 2. Scripts
- `sound_attenuation_analysis.Rmd`: R Markdown document to model sound attenuation.
- `apply_perceptibility_truncation.ipynb`: Jupyter notebook for applying perceptibility truncation to acoustic survey data.
- `negBinom_models.ipynb`: Jupyter notebook for modelling species-level relative abundance response to variable retention, after applying distance truncation.

## 3. Software Requirements
- R (version 4.0.0 or later)
- Python (version 3.7 or later)
- Jupyter Notebook
- cmdstan (version 2.32.1)

### R Packages:
- tidyverse
- glmmTMB
- ggplot2
- gridExtra
- DHARMa
- MuMIn
- caret

### Python Packages:
- pandas
- numpy
- matplotlib
- seaborn
- scikit-learn
- arviz
- cmdstanpy

## 4. Analysis Workflow
1. Data Preparation:
   - Load and merge datasets
   - Calculate mean amplitude
   - Create binary forest variable
   - Convert variables to appropriate types

2. Model Fitting:
   - Fit several glmmTMB models with different predictors
   - Compare models using AICc

3. Model Diagnostics:
   - Check residuals using DHARMa
   - Calculate R-squared values

4. Predictions and Visualization:
   - Generate predictions for open and forested habitats
   - Create plots showing attenuation patterns

5. Cross-validation:
   - Perform 10-fold cross-validation
   - Calculate RMSE and MAE

6. Perceptibility Truncation:
   - Apply distance-based truncation to abundance data
   - Compare results at different truncation distances

7. Negative Binomial Models:
   - Fit negative binomial models for species abundance
   - Compare models using WAIC
   - Evaluate model fit using diffenret truncation distances using loo and plots
   - Model evaluation with R-hat & trace plots
"# Limited-amplitude-retention" 
