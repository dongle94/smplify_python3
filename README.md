I modified original repo(https://github.com/githubcrj/simplify) to work in python3.
A lot of things have changed a little bit, from installation to script testing. 


# Code, data and results for Keep it SMPL: Automatic Estimation of 3D Human Pose and Shape from a Single Image
We supply an implementation of our paper in `fit_3d.py` and a set of example scripts to visualize the results of our experiments.

To try out the demo on LSP images, follow the directions in "Getting Started".

To learn more about the project, please visit our website: http://smplify.is.tue.mpg.de

You can find the paper at: http://files.is.tue.mpg.de/black/papers/BogoECCV2016.pdf

For comments or questions, please email us at: smplify@tuebingen.mpg.de

Please cite the paper if this code was helpful to your research:

```
@inproceedings{Bogo:ECCV:2016,
  title = {Keep it {SMPL}: Automatic Estimation of {3D} Human Pose and Shape
  from a Single Image},
  author = {Bogo, Federica and Kanazawa, Angjoo and Lassner, Christoph and
  Gehler, Peter and Romero, Javier and Black, Michael J.},
  booktitle = {Computer Vision -- ECCV 2016},
  series = {Lecture Notes in Computer Science},
  publisher = {Springer International Publishing},
  month = oct,
  year = {2016}
}
```

## System Requirements

Operating system: OSX, Linux

- Python 3.x
  - I test this project in python 3.8.x

Python Dependencies:

- [Numpy & Scipy](http://www.scipy.org/scipylib/download.html)
- [Chumpy](https://github.com/mattloper/chumpy) 
- [OpenCV](http://opencv.org/downloads.html)
- [OpenDR](https://github.com/mattloper/opendr)
- [SMPL](http://smpl.is.tue.mpg.de)   

Note that installing SMPL requires registration and manual download on the SMPL website.


## Installation `Opendr` for python3
If you want to install `opendr` in python3, you should edit some code in `opendr` follow below.
`opendr`'s version doesn't matter.

```
$ pip download --no-deps opendr==0.78
$ tar zxvf opendr-0.78.tar.gz
$ cd opendr-0.78
$ sed -i 's/from _constants import/from opendr.contexts._constants import/g' ./opendr/contexts/ctx_base.pyx 
$ python setup.py install
```


## Getting Started
Currently, what is possible is a demo run on the LSP dataset.

1. Clone the code   
Clone the repo in your local environments.
   ```shell
   $ git clone https://github.com/dongle94/smplify_python3.git 
   ```

2. Create envs and install dependencies  
You should use `conda` envs. I test this project python version `3.8`.
Installation python package include `opencv` and `opendr`. 
   ```shell
   $ conda create -n smpl python==3.8.18
   $ conda activate smpl
   $ pip install -r ./requirements.txt
   ```
   And follow above [Installation Opendr](#installation-'opendr'-for-python3)

3. Get LSP data  
Original LSP dataset [Link](http://www.comp.leeds.ac.uk/mat4saj/lsp_dataset.zip) does not work.
So these original repo has LSP Images. But I modified data directory structure.
So you can skip step 3.1. below.

   1. Get the images  
   Download images from the [LSP dataset](http://www.comp.leeds.ac.uk/mat4saj/lsp_dataset.zip).   
   Make sure to download the cropped & scaled dataset, not the original scale images.  
   Unzip this directory to any location you like.

   2. Get the Deepcut detected joints  
   Download `lsp_results.tar.gz` from our [website](http://smplify.is.tue.mpg.de). 
   Extract this in the `{REPO_HOME}/` directory. Maby this creates the `smplify_python3/results/lsp/...` directory.

4. Include female/male SMPL models from the SMPL package  
You should download [`SMPL`](https://smpl.is.tue.mpg.de/) python version 1.0.0.
But I already include that models female/male(gender-specific model) model pickle(`.pkl`) in `./code/models`.
   ```
   ./code
   ├── lib
   ├── ...
   └── models
       ├── basicModel_f_lbs_10_207_0_v1.0.0.pkl
       ├── basicmodel_m_lbs_10_207_0_v1.0.0.pkl
       ├── basicModel_neutral_lbs_10_207_0_v1.0.0.pkl
       ├── gmm_08.pkl
       ├── regressors_locked_normalized_female.npz
       ├── regressors_locked_normalized_hybrid.npz
       └── regressors_locked_normalized_male.npz
   ```

5. Run fit_3d.py  
You can run the demo script now by typing the following
   ```shell
   # PYTHONAPTH=./ python ./code/fit_3d.py {base_dir} {out-dir} {--gender-neutral} {--viz}
   $ PYTHONAPTH=./ python ./code/fit_3d.py ./ --out-dir=./out --viz
   ```

   The script fits the model to LSP data, saving the results in `./out/`.

Folder structure
==========
1. `smplify_public/code/` contains the main script running the fitter and visualization utilities (in python and MATLAB) to inspect the results (see also the following Section)
2. `smplify_public/code/lib` provides scripts implementing the objectives minimized during fitting
3. `smplify_public/code/models` provides additional data needed to run the fitter:   
   `gmm_08.pkl` stores the mixture of Gaussians info   
   `lbs_tj10smooth6_0fixed_normalized_locked_hybrid_model_novt.pkl` stores the gender-neutral SMPL model   
   `regressors_locked_normalized_*.npz` store the per-gender regressors to obtain capsule axis length and radius from shape parameters. 

Results
==========
On the [website](http://smplify.is.tue.mpg.de), we provide the detected joints and our fit results for LSP, HumanEva-I and Human3.6M. The following sections describe this data and how to use it.

DeepCut joints
--------------------
2D joints detected with DeepCut are saved as `est_joints.npz` in each dataset/sequence directory.
It holds 3 x 14 x N, where each column stores the (x, y, confidence)
value for the corresponding joint (see below for joint definition).  N is the number of images in the dataset (or frames in the
sequence). I.e. for LSP: N = 2000, where 0-999 is training data, 1000-1999 is test data.
For HEva/H36M: N = number of frames in the sequence.

The 14 DeepCut joints correspond to:

|index     |  joint name      |    corresponding SMPL joint ids  |
|----------|:----------------:|---------------------------------:|
| 0        |  Right ankle     |8                                 |
| 1        |  Right knee      |5                                 |
| 2        |  Right hip       |2                                 |
| 3        |  Left hip        |1                                 |
| 4        |  Left knee       |4                                 |
| 5        |  Left ankle      |7                                 |
| 6        |  Right wrist     |21                                |
| 7        |  Right elbow     |19                                |
| 8        |  Right shoulder  |17                                |
| 9        |  Left shoulder   |16                                |
| 10       |  Left elbow      |18                                |
| 11       |  Left wrist      |20                                |
| 12       |  Neck            |-                                 |
| 13       |  Head top        |vertex 411 (see line 233:fit_3d.py) |

SMPL does not have a joint corresponding to the head top so we picked an
appropriate vertex as the corresponding point for the head.

Fits
--------------------
We save the parameters of our fits in a pickle file `all_results.pkl` for each sequence/dataset.
This includes the shape coefficients, pose parameters, camera translation, as well as the internal camera parameters we used such as focal length and principal point.

Please see `show_humaneva.py` to learn how to use these results to set the shape and pose of SMPL.

For Human3.6M, we used a cropped version of the images (using a padded rectangle
on background subtraction) for efficiently running the joint detector. We save
this crop information as 'crop_box' in this pickle file. 

We also save the mesh information (vertices and faces) of the fitted model in
`meshes.hdf5` for each sequence/dataset. Please use `visualize_mesh_sequence.py`
or `visualize_mesh_sequence.m` for MATLAB version to see these meshes. 

Notes
======
- Code: Note that the public code is a demo code which doesn't include the C++ implementation used to compute SMPL derivatives with respect to pose and shape in the paper. For this reason it runs ~3x slower.

- LSP: We provide two results, one with gendered SMPL model `results/lsp/all_results.pkl` and
another with gender-neutral model `results/lsp/all_results_gender-neutral.pkl`. 

- Human3.6M: The camera we used is 'cam_3'. We experimented on all frames of the 15 actions.