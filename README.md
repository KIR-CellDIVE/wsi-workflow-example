# Example segmentation and analysis workflow

Here, we provide an example workflow starting from segmentation to spatial analysis utilising the [segmentation](https://github.com/KIR-CellDIVE/wsi-segmentation) and [analysis](https://github.com/KIR-CellDIVE/wsi-analysis) steps of DIVE-MAP. This also includes comparisons to a QuPath-based segmentation approach. Moreover, this repository reproduces some of the figures and results shown in the accompanying STAR protocol publication (doi: TO BE ADDED).

## Installation

### Setup system for building the whole-slide image analysis container
Please follow the steps outlined here [here](https://github.com/KIR-CellDIVE/wsi-analysis) to prepare your computer for building and running the whole-slide image analysis container. You only have to setup you system once to build the various containers that are part of [DIVEMAP](https://github.com/KIR-CellDIVE/DIVE-MAP)

### Build whole-slide image analysis container

If you on Windows enter your previously created WSL virtual environment by typing `wsl -d Ubuntu -u ubuntu` (if you have not already done so) or if you on Linux open your favourite terminal emulator. To build the WSI segmentation container we start by creating a `builds` folder in the HOME `~` directory and cloning/downloading this repository from GitHub: 


```bash
mkdir -p ~/builds \
&& cd ~/builds \
&& git clone https://github.com/KIR-CellDIVE/wsi-workflow-example.git
```
Next, we build a Apptainer container called `wsi_segmentation_comparison.sif` based on definition file `Apptainer`:

```bash
cd wsi-workflow-example/apptainer \
&& sudo apptainer build wsi_segmentation_comparison.sif Apptainer
```

In order to make it easier to run the container in the future we create a bash scripts `wsi-analysis` in `~/.local/bin` that can simply be called from anywhere inside the console. Adapt these commands if you decide to download and build the container in a different directory. (Skip this step if you'd rather start the containers directly yourself). 

We make sure that `~/.local/bin` exists.
```bash
mkdir -p ~/.local/bin
```
Then, we create two bash scripts in `~/.local/bin` to make starting the container to run the analysis more straightforward.


```bash
echo "#! /bin/bash
## run wsi_segmentation_comparison
[ -d "/mnt" ] && apptainer run --writable-tmpfs \"\$@\" --bind /mnt:/opt/analysis/drives --bind /:/opt/analysis/host $HOME/builds/wsi-workflow-example/apptainer/wsi_segmentation_comparison.sif || apptainer run --writable-tmpfs \"\$@\" --bind /:/opt/analysis/host $HOME/builds/wsi-workflow-example/apptainer/wsi_segmentation_comparison.sif" > ~/.local/bin/wsi_segmentation_comparison
```
Lastly, we make these two bash scripts executable

```bash
chmod +x ~/.local/bin/wsi_segmentation_comparison
```
and reload the `~/.profile` file to add `~/.local/bin` to `$PATH`.
```bash
source ~/.profile
```



## Run notebooks

If you have followed the installation step you should be able to run the segmentation, analysis and QuPath-vs-Deepcell comparison notebooks found in the `notebooks` subfolder in this repository. For the QuPath-vs-Deepcell comparison we have already include example results following segmentation and quantification with the respective methods. In order to run the segmentation and analysis notebook you have to download the three example whole slide image from  [DOI 10.5281/zenodo.14926053](10.5281/zenodo.14926053) and place them into the `ome_tiff` folder alongside the `notebooks` folder containing the jupyter notebook. 

If you are on `Windows` and you use `WSL`, first open `PowerShell` and enter the previously created WSL environment `Ubuntu_DIVEMAP` as the user `ubuntu` if you haven't already done so:

```bash
wsl -d Ubuntu_DIVEMAP -u ubuntu
```

If you have successfully set up the `Apptainer` containers for [segmentation](https://github.com/KIR-CellDIVE/wsi-segmentation) and [analysis](https://github.com/KIR-CellDIVE/wsi-analysis) as well as the one in the previous section, you should have the following 3 commands available to start the respective Jupyter Lab servers: `wsi-segmentation-gpu` (or `wsi-segmentation`), `wsi-analysis` and `wsi_segmentation_comparison`.

Each notebook has slightly different computational requirements and thus has to be open with a specific container/Jupyter Lab server:

- The [1_WSI_Deepcell_Segmentation](https://github.com/KIR-CellDIVE/wsi-workflow-example/blob/main/notebooks/1_WSI_Deepcell_Segmentation.ipynb) notebook can be opened after running `wsi-segmentation-gpu` (or `wsi-segmentation`)
- The [2_WSI_Analysis](https://github.com/KIR-CellDIVE/wsi-workflow-example/blob/main/notebooks/2_WSI_Analysis.ipynb) notebook can be opened after running `wsi-analysis`
- The [3_Analysis_DeepCell_segmentation](https://github.com/KIR-CellDIVE/wsi-workflow-example/blob/main/notebooks/3_Analysis_DeepCell_segmentation.ipynb), [4_Analysis_QuPath_segmentation](https://github.com/KIR-CellDIVE/wsi-workflow-example/blob/main/notebooks/4_Analysis_QuPath_segmentation.ipynb), [5_Data_export](https://github.com/KIR-CellDIVE/wsi-workflow-example/blob/main/notebooks/5_Data_export.ipynb) and [6_Comparison_QuPath_Deepcell](https://github.com/KIR-CellDIVE/wsi-workflow-example/blob/main/notebooks/6_Comparison_QuPath_Deepcell.ipynb) notebooks can be opened after running `wsi_segmentation_comparison`  
