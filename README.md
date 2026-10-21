# CERR Segmentation Model Installer

Locally set up and install network weights, scripts and Python environments for running AI-based image segmentation and registration models.

```
      ___           ___           ___           ___     
     /  /\         /  /\         /  /\         /  /\    
    /  /:/        /  /:/_       /  /::\       /  /::\   
   /  /:/        /  /:/ /\     /  /:/\:\     /  /:/\:\  
  /  /:/  ___   /  /:/ /:/_   /  /:/~/:/    /  /:/~/:/  
 /__/:/  /  /\ /__/:/ /:/ /\ /__/:/ /:/___ /__/:/ /:/___
 \  \:\ /  /:/ \  \:\/:/ /:/ \  \:\/:::::/ \  \:\/:::::/
  \  \:\  /:/   \  \::/ /:/   \  \::/~~~~   \  \::/~~~~ 
   \  \:\/:/     \  \:\/:/     \  \:\        \  \:\     
    \  \::/       \  \::/       \  \:\        \  \:\    
     \__\/         \__\/         \__\/         \__\/    
 
Medical Physics Department, Memorial Sloan Kettering Cancer Center, New York, NY
 
Welcome to the CERR segmentation model installer! For usage information, run with -h flag
 
Usage Information: 
	Flags: 
		-i : Flag to run installer in interactive mode (no argument)
		-m : [1-9] Integer number to select model to install. For list of available options, see below. 
		-d : Directory to install model with network weights 
		-p : [P/C/N/U] Setup and install Python environment P: setup Conda env from python requirements.txt; C: Conda pack download; U: Setup uv env from Python requirements.txt; N: No install. 
		-n : [1-9] Print the model name of number argument
	    -r : Provide URL to tar archive for model weights (optional)
		-u : User credentials for private GitHub repo, format is "user:token"
		-h : Print help menu 
 
The following are the list of available models. When passing the argument to installer, select the number of the model to download: 
          	1.  CT_cardiac_structures_deeplab
          	2.  CT_LungOAR_incrMRRN
          	3.  MR_Prostate_Deeplab
          	4.  CT_Lung_SMIT
          	5.  MRI_Pancreas_Fullshot_AnatomicCtxShape
          	6.  CT_HeadAndNeck_OARs
          	7.  CT_HN_SMIT
          	8.  CT_HeartSubstruct_SMIT
          	9.  CT_WHOLEBODY_SMITplus
          	10.  MR_Rectum_GTV_SMIT
          	11.  MR_HN_Nodule_SMIT
          	12.  CT_Lung_OAR_SMITplus
		13. MR_BrainMets_SMITplus
      
     
``` 
  
## Model Repository organization

Model repositories must be structured as follows for installation and deployment via [pyCERR](https://github.com/cerr/pyCERR.git). 

```text
{ModelName}/  
├── {inference_script}.py        # Required for use with pyCERR. Must be placed at root.  
├── uv_config.txt                # Optional  
├── requirements/
│   └── req1.txt                 # Required for use with pyCERR
├── weights/
│   └── {model_name}.txt	 # Required (one per model) 
└── {library_subdir}/              
    ├── __init__.py              # Required  
    └── *.py 

YAML specification files distributed with pyCERR contain the information (pre and post-processing, inference entrypoint, input parameters, output labels) needed to run the model via pyCERR. 
```

  
* ### {model_name}.txt

Contains the URL to model weights, base64+gzipped.
```
  MODEL_WEIGHTS <encoded-url>
```

* ### uv_config.txt

Contains the Python version and any extra install flags for building the *uv* virtual environment.  

**Example**
```
  python_version=3.8
  uv_flags=--find-links https://download.pytorch.org/whl/torch_stable.html --no-deps
```

* ### requirements/*.txt

Standard pip requirements files. All .txt files in `requirements/` are installed automatically. 


* ### YAML run specs

Defines how the model is run and what outputs it produces.

```
metadata:
    model_name: "{ModelName}"
    version: "{ver}"
    description: "{desc}"

preprocessing:
    script: "{pycerr_preprocessing_script}.py"

postprocessing:
    script: "{pycerr_postprocessing_script}.py"

outputs:
    "*_AI_seg.nii.gz":       # Pattern for output files
        1: "Structure A"     # Label number to structure name mapping
        2: "Structure B"

execution:
        entrypoint: "{inference_script}.py"
        arguments:
            - name: "input_path"      # Positional: passed by position
              type: "positional"
              required: true
            - name: "results_dir"     # Flag: passed as --results_dir value
              type: "flag"
              flag_string: "--results_dir"
              required: true
            - name: "use_tta"         # Boolean flag: appended only when true
              type: "boolean_flag"
              flag_string: "--use_tta"
              required: false
              default: true
```

