![](https://github.com/larryhernandez/MRI_research/blob/master/Brain_FSPGR_Sagittal_158.jpg)


# Description of this Repository
This [repository](https://github.com/larryhernandez/MRI_research) contains MATLAB functions and scripts, as well as Linux C-shell scripts, that I developed to facilitate image reconstruction for a novel, 3D Rosette-like MRI sampling pattern called icones.

The code in this repository would achieve the following tasks if provided with appropriate raw MRI data file(s):
  1. Extract raw imaging data and calibration data.
  2. Generate 3D coordinates for each MRI data sample collected. In MRI terminology this is known as a 3D k-space map. These coordinates are needed for creating images from data that have been collected with a non-Cartesian sampling pattern such as icones.
  3. Create a crude estimate of the density compensation coefficients needed for image reconstruction, OR create a symbolic link to a file that contains previously generated density compensation coefficients.
  4. Utilize calibration data to restore signal loss and to reduce image blurring caused by eddy currents.

# How to Use This Code
Disclaimer: I am not at liberty to provide any raw imaging data nor the additional software required to convert that data into images. However, I will provide a brief description on how to use this code anyway.

The main script that controls the reconstruction is called 'run_iconereconfx.m'. If set up properly, the script will reconstruct images from several raw data files, each of which must be stored in its own directory. The script accesses a file, called 'ReturnStructures.m' which must be updated for each new MRI data file. 'ReturnStructures.m' initializes two primary structures containing information about the data acquisition and desired image reconstruction parameters.

To reconstruct images, update ReturnStructures.m and run_iconereconfx.m:  
  1. ReturnStructures.m  
    a. Create a new �case� number for the new image acquisition pfile. Each imaging exam should have its own directory.  
    b. Specify the directories containing the icones gradient waveforms, rotation matrices, and density compensation file  
    c. Specify the directory containing the calibration data (i.e. �b0datapath_x�)  
    d. Specify values for any of the following binary flags. The default values are listed  
      1. reconstruction.grid_psf: 	     	0 if not generating an impulse response
      2. reconstruction.write_custom_kweight: 1 to generate a crude density compensation
      3. reconstruction.cplusplus_recon:      1 to utilize C++ gridding software, 0 for the C-based version (produces dicom images)
      4. reconstruction.use_bin_grad_files:   1 to use the gradient files that the scanner uses or 0 for the files generated by MATLAB
      5. reconstruction.use_calgrad:          0 to disable calibration correction method  

  2. run_iconereconfx.m  
    a.Specify values for the following variables:  
      1. echoes2process: 		     0 (first echo only); 1(second echo only); 2 (only the radial spokes of both echoes); 3 (both echoes)
      2. experiments_to_run: 		     integer corresponding to the case number listed in �ReturnStructures.m�
      3. recon_without_b0corr:             0 or 1. If 0 will reconstruct images before applying B0 correction (for debugging)

# Background: Description of the 'icones' 3D Rosette-like MRI Data Sampling Pattern
Utilizing image reconstruction software to create images is one thing. However, it makes sense to have a basic understanding of how that data was collected in the first place. This is the section where I provide a very brief synopsis of the data collection scheme for this 3D icones scan.

For those unfamiliar with magnetic resonance imaging, the data acquisition scheme can be interpreted as a process of collecting data points from a two-dimensional (2D) or three-dimensional (3D) space. Altogether, this collection of points is referred to as a "sampling pattern". Sometimes sampling patterns resemble familiar shapes when drawn on paper. Examples include a spiral, a rectangular grid of points (aka Cartesian sampling pattern), or a collection of straight lines that are arranged much like the spokes of a wheel (aka radial scanning).

In simplest terms, the icones sampling pattern can be represented by a two-dimensional rosette-like petal (Figure 1a). To generate the full 3D sampling pattern, this petal is strategically rotated into 3D space (Figure 1b) using special equations. Figure 1b depicts a sequential ordering of these rotations, starting at the south pole (-kz) and spiraling upward to the north pole (+kz). However, a non-sequential or random ordering of these rotations is also possible.

The 3D icones sampling pattern was designed for rapid, three-dimensional, high resolution magnetic resonance imaging of the body. Specific applications of 3D icones include visualizing the cartilage of the knee and hip (to detect cartilage lesions caused by acute injury or osteoarthritis), whole brain imaging (tumor detection), and bilateral breast imaging (tumor / lesion detection and characterization).


Figure 1a: 2D icone petal  |Figure 1b: Full 3D icones Sampling Pattern
:-------------------------:|:-------------------------:
![k-Space units are m^{-1}](https://github.com/larryhernandez/MRI_research/blob/master/Figure_1a_2D_icones_petal.jpg)  |  ![](https://github.com/larryhernandez/MRI_research/blob/master/Figure_1b_icones_animated_3Dsampling.gif)


# Select Images


## Quality Assurance Phantom: Visual Verification that the Calibration Technique Improves Image Quality
As previously mentioned, a calibration method was devised and employed to reduce blurring and restore signal losses that are caused by unwanted eddy currents that arise during the scan. The details of this correction scheme are omitted here; however, I have provided two images of a quality assurance phantom to demonstrate that this calibration method is effective. It is apparent from the images below that this method does a great job of correcting image artifacts. In the corrected image labeled "After" the edges of the phantom are depicted accurately (green and yellow arrows), a grey blurry "fog" (red arrow) that once covered a large region of the image has been removed, and (white) signal has been restored across the entire image.


          Before           |  After
:-------------------------:|:-------------------------:
![](https://github.com/larryhernandez/MRI_research/blob/master/ACR_phantom_without_calibration.jpg)  |  ![](https://github.com/larryhernandez/MRI_research/blob/master/ACR_phantom_calibration.jpg)


## Brain Scanning with 3D icones

It's great that the scan and reconstruction worked well on a phantom, but what about on an actual human being? Well, after the calibration method was developed, tested, and properly implemented, the brain of a live human was scanned. The final images are displayed below.  


          Axial            |     Sagittal Reformat
:-------------------------:|:-------------------------:
![](https://github.com/larryhernandez/MRI_research/blob/master/Brain_FSPGR_Axial_150.jpg)  |  ![](https://github.com/larryhernandez/MRI_research/blob/master/Brain_FSPGR_Sagittal_158.jpg)


Note: While you see two distinct views of the brain, only one scan was utilized to collect the imaging data. That is the beauty of a 3D MRI scan which acquires isotropic voxels. The volunteer was scanned only once using an axial scan plane. From this axially acquired data a stack of 400 images with an axial view were generated (only one axial image is displayed here). After the axial images were generated, they were opened using standard image visualization software. In this case, the image visualization software that I utilized was ImageJ. These images were then reformatted with this imaging visualization software to generate 400 images with a view from the Sagittal plane. Pretty cool, huh?!

## Knee Scan with 3D icones

The 3D icones sampling pattern was also implemented for imaging the knee. The MRI physics employed for this type of tissue contrast is different from what was used for imaging the brain. Those details will be omitted here, but essentially the software that controls the scanning process is slightly revised to account for the relevant physics. The image reconstruction software, however, remains the same. As you can see from the images below, it is possible to use the icones sampling pattern and corresponding image reconstruction software for imaging the human knee.


         Axial             |         Sagittal	       |           Coronal
:-------------------------:|:-------------------------:|:-------------------------:
![](https://github.com/larryhernandez/MRI_research/blob/master/Knee_FSATR_Axial_168.jpg)  |  ![](https://github.com/larryhernandez/MRI_research/blob/master/Knee_FSATR_Sagittal_141.jpg) | ![](https://github.com/larryhernandez/MRI_research/blob/master/Knee_FSATR_Coronal_219.jpg)