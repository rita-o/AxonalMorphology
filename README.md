
## REFERENCE:

This repository includes all the analysis scripts related to the scientific article:  
*In-vivo estimation of axonal morphology from Magnetic Resonance Imaging (MRI) and electroencephalography (EEG) data*  
Rita Oliveira, Andria Pelentritou, Giulia Di Domenicantonio, Marzia De Lucia, Antoine Lutti
https://www.frontiersin.org/articles/10.3389/fnins.2022.874023/full

The main objective is to use data collected in-vivo in humans to estimate microscopic morphologic features of the white matter tracts.

The in-vivo data used in the proposed approach is:
- the MRI g-ratio sampled along the visual transcallosal white matter tract
- a conduction velocity estimated from an EEG measure of interhemispheric transfer time (IHTT)

The microscopic morphologic features of white matter we estimate with the proposed approach are:
- the axonal radius distribution, P(r)
- the g-ratio dependence on the radius, g(r)


## AUTHORS:

Author: Rita Oliveira, PhD student  
Co-supervisor: Marzia De Lucia  
Supervisor: Antoine Lutti 

Laboratory for Research in Neuroimaging (LREN)  
Department of Clinical Neuroscience,  
Lausanne  University Hospital and University of Lausanne  
Mont-Paisible 16, CH-1011 Lausanne, Switzerland

Email: rita.oliveira.uni@gmail.com


Last updated: January 2021

## CONTENT:

This package includes:
   - The analysis codes for processing of EEG data
   - The analysis codes concerning the proposed model for estimating in-vivo microstructural features of white matter tracts

## DEPENDENCIES (not provided here):

- eeglab13_4_4b (https://sccn.ucsd.edu/eeglab/download.php)
- fieldtrip-20191206 (https://www.fieldtriptoolbox.org/download/)
- brainstorm3 (16/04/2021) (https://neuroimage.usc.edu/bst/download.php)
- spm12 (https://www.fil.ion.ucl.ac.uk/spm/software/download/)
- Optimization Toolbox - from MATLAB (https://www.mathworks.com/products/optimization.html)
- Statistical and Machine Learning Toolbox - from MATLAB (https://www.mathworks.com/products/statistics.html)
- inputsdlg function(https://www.mathworks.com/matlabcentral/fileexchange/25862-inputsdlg-enhanced-input-dialog-box)
- peakseek function (https://www.mathworks.com/matlabcentral/fileexchange/26581-peakseek)

These analysis scripts were run under MATLAB 2021a.

## INPUT DATA (not provided here):

The initial data necessary to be able to run this analysis includes:
- The raw data EEG (in the format block_X.TRC; and metrics obtained during the implemented visual paradigm)
- The digitization file containing the position of the electrodes on the EEG cap (SubjectName.elec)
- MRI g-ratio sampled along the visual transcallosal tract in matrix format (G_ratio_samples.mat)
- The length of the visual transcallosal tract in matrix format (Tract_length.mat)
- Freesurfer output files (segmented structural image as obtained from Freesurfer: lh.BA_exvivo.annot, rh.BA_exvivo.annot, BA_exvivo.ctab, T1.mgz, rh.pial, lh.pial)
	
## **DATA FOLDER ORGANIZATION:**

The folder structure looks like this:

- Subjects
  - SubjectName
    - EEG
      - Originals  
      - Matlab_Outputs
      - Preprocessed      
      - Delays 
      - Statistics_Source
    - MRI
      - MicrostructureEstimation
      - Freesurfer
-  Study_Group_Results

The majority of the folders are created along the scripts, with exception
of the folders containing the data mentioned in the previous section.


## DESCRIPTION OF THE MAIN ANALYSIS STEPS:

- **SET PATHS** 
	Creates the path structure used in all the other scripts. 
User needs to edit this script and change the path variables 
to match the ones on the user's local computer.
		
- **SET PARAMETERS**
Creates the parameters needed to run the analysis. It defines the subjects to run, the conditions used on the EEG paradigm, the structure with the parameters for EEG preprocessing and the structure with the parameters for EEG statistical cluster permutation analysis. User needs to edit this script and change the parameters to the desired ones.

	*To replicate the results of the associated scientific article, given that the IHTT concerned only the 'Left' visual stimulation, cond_list is {'Left'} (set by default).*

		
- **EEG - PREPROCESSING**
Preprocessing of EEG data with Fieldtrip and EEGlab software. This script assumes that the data was acquired under different blocks/runs and with different conidtions (e.g. presentation of visual stimulus on the Right or Left hemifield). User input is needed as detailed in the function matlab code. This script also prepares the data in a format that can be used in Brainstorm software (for the step: EEG - SOURCE RECONSTRUCTION). EEG raw data is required in this step.
		
	*If the the user wants to use this script with their own data, take attention to the fact that the first call inside this function ('readdata') is highly dependent on the type of data used in this analysis (e.g. format 'block_*.TRC', micromed EEG system, names of the electrodes saved on .TRC file, log file created during the EEG stimulus presentation) and the triggers values used during the EEG paradigm (for further information, the user is forward to the scientific paper associated with these scripts).*	
		
		
- **EEG - SOURCE RECONSTRUCTION**
Source reconstruction analysis with Brainstorm software. By using MRI data acquired on the same subjects, it performs source reconstruction on the signals obtained in the previous step. It also extracts the current source density values inside a given region of interest within the brain and prepares the data in a format that can be used in Matlab easily (for the step: EEG - IHTT ) User input is needed as detailed in the function matlab code. Freesurfer output files are needed in this step.

	*To replicate the results of the associated scientific article, the region of interest is the region containing the primary and secondary visual cortices - V1V2 - on the left or right brain hemispheres (set by default).*
	
		
- **EEG - CLUSTER PERMUTATION**
Performs cluster permutation statistics on the current source density values obtained in the source reconstruction (step: EEG - SOURCE RECONSTRUCTION), within a given region of interest. 

	*To replicate the results of the associated scientific article, the region of interest is the region containing the primary and secondary visual cortices - V1V2 - on the left or right brain hemispheres (set by default).*
		
- **EEG - IHTT ESTIMATION**
Estimates the IHTT, in ms, from the group average current source density, obtained in the previous step (EEG - SOURCE RECONSTRUCTION). Using sub-groups of the available subjects, calculates a standard deviation, in ms, around the estimated IHTT. This will be used to define a confidence interval on the IHTT estimation that will be used on the next step (BIOPHYSICAL MODEL OF MICROSTRUCTURE) to provide a confidence interval on the microstructural estimates obtained.

	*To replicate the results of the associated scientific article, given that the IHTT concerned only the 'Left' visual stimulation, cond_list is {'Left'} (set by default).*
		
- **BIOPHYSICAL MODEL OF MICROSTRUCTURE**
Estimates microstructural features of the white matter: the axon radius distribution, P(r), and the g-ratio dependence on the radius, g(r). MRI g-ratio estimates sampled along a white matter tract and the corresponding tract length are needed in this step, along with the IHTT estimated in step EEG - IHTT ESTIMATION.

	*To replicate the results of the associated scientific article, the tract to be used is the visual transcallosal tract, connecting the primary and secondary visual areas (V1V2) from both hemispheres and the delay is the IHTT (using a left visual field stimulation). The script assumes as default that the 'G_ratio_samples.mat' and 'length.mat' were obtained on this tract and loads the IHTT estimated in the previous step (EEG - IHTT ESTIMATION).*		  

## HOW TO USE:

Important: the analysis scripts need to be run from the 'main' scripts folder.

The first step is to set your local data paths by editing and running the script:
> Set_paths()

Then edit and run the following script to define the parameters for your analysis:
> Set_params()

The EEG analysis consists of several parts that can be run independently 
as long as the previous step was performed:
> EEG_Step1_Preprocessing()        - requires manual input to clean the EEG data

> EEG_Step2_SourceReconstruction() - requires outputs from EEG_Step1_Preprocessing as well manual input on Brainstorm

> EEG_Step3_ClusterPermutation()   - requires output files from EEG_Step2_SourceReconstruction

> EEG_Step4_IHTT()                 - requires output files from EEG_Step2_SourceReconstruction

The biophysical model of microstructure can finally be run with:
> MODEL()                          - requires output files from EEG_Step4_IHTT 


## NOTE:

The data used in the original publication is publicly available [here](https://doi.org/10.5281/zenodo.6027335).

The analysis steps EEG - IHTT ESTIMATION and BIOPHYSICAL MODEL OF MICROSTRUCTURE can be run on the provided data.

