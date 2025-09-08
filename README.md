# [LS-HDIB Dataset](https://kaustubh-sadekar.github.io/LS-HDIB/)
LS-HDIB: A Large Scale Handwritten Document Image Binarization Dataset
## Setup and Install

```script
git clone https:/kulprb/github.com//LS-HDIB.git
cd LS-HDIB/
pip install -r requirements.txt
```
## Run segmentation for your own image

```script
python run.py cpu input_2.jpg unet_best_weights.pth
```

The output file will be saved as `<INPUT_FILE_NAME>_output.jpg`. For this specific example it will be input_2_output.jpg

*For better understanding of the input arguments type `python run.py -h`*


Method 	Feature 	Classifier 	Data Set Size 	Accuracy (%) 
Govindraju et al. [18] 	Gradient 	Neural Networks 	4,506 	84 
Kompalli et al. [19] 	GSC 	Neural Network 	32,413 	84.77 
Bansal et al. [16] 	Statistical Structural 	Statistical 
Knowledge Sources 	Unspecified 	87 
Huanfeng Ma et al. [20] 	Statistical Structural 	Hausd or off image comparison 	2,727 	88.24 
Natrajan et al. [25] 	Derivatives 	HMM 	21,727 	88.24 
Bansal et al. [17] 	Filters 	Five filters 	Unspecified 	93 
Dhurandhar et al. [23] 	Contours 	Interpolation 	546 	93.03 
Kompalli et al. [22] 	GSC 	K-nearest neighbor 	9,297 	95 
Kompalli et al. [21] 	SFSA 	Stochastic finite state automa- ton 	10,606 	96 
Dhingra et al. [24] 	Gabor 	MCE 	30,000 	98.5 
Bhavesh  et al. 	Gradient 	Stochastic 
Gradient Descent 
(SGD) 	34,215 	98.64 
 
Table 7. Result comparison of OCR Systems for Printed Devanagari words 
Method 	Feature 	Classifier 	Data Set Size 	Accura cy (%) 
Govindraju et al. [18] 	Gradient 	Neural Networks 	4,506 	53 
Kompalli et al. [22] 	GSC 	K-nearest neighbor 	1,882 	58.51 
Kompalli et al. [19] 	GSC 	Neural Network 	14,353 	61.8 
Ma et al. [20] 	Statistical Structural 	Hausdorff image comparison 	2,727 	66.78 
Kompalli et al. [21] 	SFSA 	Stochastic finite state automaton 	10,606 	87 
Bhavesh  et al. 	Gradient 	Stochastic Gradient Descent (SGD) 	15,455 	97.3 
 
