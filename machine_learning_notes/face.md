[Up](index.md)

# Face
## Toolkit

- [4dface.org](http://www.4dface.org/)
- OpenFace: an open source facial behavior analysis toolkit - Tadas Baltrušaitis, Peter Robinson, and Louis-Philippe Morency, in IEEE Winter Conference on Applications of Computer Vision, 2016

## Face Recognition

* SphereFace: Deep Hypersphere Embedding for Face Recognition [paper](https://www.google.com.hk/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0ahUKEwiNu4ex7O7WAhVDO7wKHQ7qCv0QFggkMAA&url=https%3A%2F%2Farxiv.org%2Fabs%2F1704.08063&usg=AOvVaw0vTkb6ot07wiuDegIInbjy)

## Face Detection

* Real-Time Rotation-Invariant Face Detection with Progressive Calibration  Networks - Xuepeng Shi, Shiguang Shan, Meina Kan, Shuzhe Wu, Xilin Chen - 2018 [paper](https://arxiv.org/abs/1804.06039v1)
  * Real-Time Rotation-Invariant Face Detection with Progressive Calibration Networks - jiwoopapa [blog](https://steemit.com/kr/@jiwoopapa/real-time-rotation-invariant-face-detection-with-progressive-calibration-networks)
  * Real-Time Rotation-Invariant Face Detection with Progressive Calibration Networks [github](https://github.com/Jack-CV/PCN)

## Facial landmark detection and tracking

- Constrained Local Neural Fields for robust facial landmark detection in the wild - Tadas Baltrušaitis, Peter Robinson, and Louis-Philippe Morency.  in IEEE Int. Conference on Computer Vision Workshops, 300 Faces in-the-Wild Challenge, 2013.  
- Joint Face Detection and Alignment using Multi-task Cascaded Convolutional Neural Networks [paper](https://www.google.com.hk/url?sa=t&rct=j&q=&esrc=s&source=web&cd=2&cad=rja&uact=8&ved=0ahUKEwirurzB6u7WAhXFbrwKHeprCe8QFggrMAE&url=https%3A%2F%2Farxiv.org%2Fabs%2F1604.02878&usg=AOvVaw1yLLG1yg9ngKZqM2oGTAkX)

## Eye gaze tracking

- Rendering of Eyes for Eye-Shape Registration and Gaze Estimation - Erroll Wood, Tadas Baltrušaitis, Xucong Zhang, Yusuke Sugano, Peter Robinson, and Andreas Bulling in IEEE International. Conference on Computer Vision (ICCV),  2015 

## Facial Action Unit detection

- Cross-dataset learning and person-specific normalisation for automatic Action Unit detection - Tadas Baltrušaitis, Marwa Mahmoud, and Peter Robinson in Facial Expression Recognition and Analysis Challenge, IEEE International Conference on Automatic Face and Gesture Recognition, 2015


## 3D Face Reconstruction

- A Morphable Model For The Synthesis Of 3D Faces - 1999 [paper](http://gravis.dmi.unibas.ch/publications/Sigg99/morphmod2.pdf) 
  - Slow, Non-convex optimization problem


- Estimating 3D Shape and Texture Using Pixel Intensity, Edges, Specular Highlights, Texture Constraints and a Prior - 2005 [paper](http://gravis.dmi.unibas.ch/publications/CVPR05_Romdhani.pdf)
  * Slow, Non-convex optimization problem
- 3D face reconstruction from a single image using a single reference face shape - 2011 [paper](https://www.ncbi.nlm.nih.gov/pubmed/21193812)
  - 2.5D, Non-convex optimization problem, Difficulty.
- Face Reconstruction in the Wild - 2011 [paper](https://grail.cs.washington.edu/3dfaces/paper.pdf)
  - Solved many problems.
- Improving 3D Facial Action Unit Detection with Intrinsic Normalization - 2015 [paper](http://www.cs.technion.ac.il/~ron/PAPERS/Conference/YudinSelaWetzlerKimmel-BMVC2015.pdf)
- Learning Detailed Face Reconstruction from a Single Image - 2016 [paper](https://homes.cs.washington.edu/~royorel/files/learning-detailed-face_cvpr.pdf)
- 3D Face Reconstruction by Learning from Synthetic Data - 2016 [paper](https://arxiv.org/abs/1609.04387)
- 3DFaceNet: Real-time Dense Face Reconstruction via Synthesizing Photo-realistic Face Images - 2017 [paper](https://www.arxiv-vanity.com/papers/1708.00980)
- Large Pose 3D Face Reconstruction from a Single Image via Direct Volumetric CNN Regression - 2017 [paper](https://arxiv.org/abs/1703.07834)
- 3D Face Reconstruction with Geometry Details from a Single Image - 2017 [paper](https://arxiv.org/abs/1702.05619) ***
- Unrestricted Facial Geometry Reconstruction Using Image-to-Image Translation - 2017 [paper](https://arxiv.org/abs/1703.10131), [github](https://github.com/matansel/pix2vertex)


### 3DMM

**3DMM: 3D face modelling using a 3D morphable model**

3D Morphable Models are used for face analysis because the intrinsic^본질적인^ properties of 3D faces provide a representation that is immune^면제된^ to intra-personal variations such as pose and illumination. Given a single facial input image, a 3DMM can recover 3D face (shape and texture) and scene properties (pose and illumination) via a fitting process. [site](http://cvssp.org/faceweb/3dmm/) [model download](http://cvssp.org/faceweb/3dmm/facemodels/)

License: Free for non- commercial use, Commercial license.

* 3D Morphable Model Fitting For Low-Resolution Facial Images - Pouria Mortazavian, Josef Kittler, William Christmas - 2012 [paper](http://www.ee.surrey.ac.uk/CVSSP/Publications/papers/Mortazavian-ICB-2012.pdf)

* Face Recognition in Low Resolution Using a 3D Morphable Model - Pouria Mortazavian [thesis](http://www.ee.surrey.ac.uk/CVSSP/Publications/papers/Mortazavian-PHD-2013.pdf)

* Robust face recognition by an albedo based 3D morphable model - Hu, G, Chan, CH, Yan, F, Christmas, W and Kittler, J - 2014 [paper](http://epubs.surrey.ac.uk/807514/)

* Face Analysis using 3D Morphable Models - Guosheng Hu - 2015 [thesis](http://www.ee.surrey.ac.uk/CVSSP/Publications/papers/Hu-thesis-2015.pdf)

* A Facial Symmetry Prior for Improved Illumination Fitting of 3D Morphable

  Model - Guosheng Hu, Pouria Mortazavian, Josef Kittler, William Christmas - 2013 [paper](http://www.ee.surrey.ac.uk/CVSSP/Publications/papers/Hu-ICB-2013.pdf)

* Resolution-Aware 3D Morphable Model - Guosheng Hu, Chi Ho Chan, Josef Kittler, William Christmas - 2012 [paper](http://www.ee.surrey.ac.uk/CVSSP/Publications/papers/Hu-BMVC-2012.pdf)

* A Multiresolution 3D Morphable Face Model and Fitting Framework - Patrik	Huber, Guosheng	Hu, Pouria Mortazavian, W.P. Koppen - 2015 [paper](https://www.researchgate.net/publication/285054377_A_Multiresolution_3D_Morphable_Face_Model_and_Fitting_Framework)

  * eos: A lightweight header-only 3D Morphable Face Model fitting library in modern C++11/14 [github](https://github.com/patrikhuber/eos)

* Fitting 3D Morphable Models using Local Features - Patrik Huber, Zhen-Hua Feng, William Christmas, Josef Kittler - 2015 [paper](https://arxiv.org/pdf/1503.02330v1.pdf)


## Pose

- Realtime Multi-Person 2D Pose Estimation using Part Affinity Fields [paper](https://www.google.com.hk/url?sa=t&rct=j&q=&esrc=s&source=web&cd=2&cad=rja&uact=8&ved=0ahUKEwjKnsKq6O7WAhXFXbwKHZ9iB_YQFggrMAE&url=https%3A%2F%2Farxiv.org%2Fabs%2F1611.08050&usg=AOvVaw2WbHoISe-y-1kPw-YR88Hy) 
- Hand Keypoint Detection in Single Images using Multiview Bootstrapping [paper](https://www.google.com.hk/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0ahUKEwisp-L66O7WAhWMvrwKHbLSBNcQFggnMAA&url=https%3A%2F%2Farxiv.org%2Fabs%2F1704.07809&usg=AOvVaw0VkheeM_5dtwGlU5oqXtua) 
- Convolutional pose machines [paper](https://www.google.com.hk/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0ahUKEwjYosqM6e7WAhXJvLwKHXpECq4QFggnMAA&url=https%3A%2F%2Farxiv.org%2Fabs%2F1602.00134&usg=AOvVaw0UofcH2X5xlsKnZHNM0k8Q) ​