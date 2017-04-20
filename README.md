# ecallen
Python package for retrieval and analysis of data from the Allen Institute for Brain Science. Includes routines to interact with the Allen's API, download images, basic image processing, and provides fast 3D registration to the Common Coordinate Framework. The essential features are illustrated in the figure below:

![](https://i2.wp.com/efferencecopy.net/wp-content/uploads/2017/04/post_5_fig_5.jpg?w=1170&ssl=1)*__Figure 1: __ The steps in acquiring and analyzing data from the Allen Institute are shown above. The tools from ecallen necessary for each step are shown below.*

Please visit [efferencecopy.net](https://efferencecopy.net) for a full explanation of the package's functionality including detailed examples of:

1. [Interaction with the API](https://efferencecopy.net/ecallen-a-python-package-for-the-allen-institutes-api/)
1. [Image segmentation](https://efferencecopy.net/allen-brain-image-segmentation-to-extract-neuron-cell-bodies/) to extract cell bodies from raw images
1. [Fast image registration](https://efferencecopy.net/allen-brain-fast-image-registration/) for aggregation across images and brains

## Table of Contents

1. [Installation](## Installation)
1. [Usage](##Usage)
    1. [Retrieve image IDs](###Retrieve image IDs)
    1. [Image Download](###Image Download)
    1. [Image Metadata](###Retrieve image metadata)
    1. [Neuron segmentation](###Neuron segmentation)
    1. [3D (whole-brain) image registration](### Fast 3D registration)
1. [Contributing](##Contributing)
1. [License](##License)


## Installation

Clone the repository. Add the ecallen directory to your Python path and import as you normally would.

```Python
# add ecallen package to Python path temporarily
import sys
sys.path.insert(0, "/path/to/ecallen")

# import the "image" module from the ecallen package
from ecallen import images as ecimg

```

## Usage

### Retrieve image IDs

```Python

""" Use ecallen to retrieve all image_ids for an experiment """

from ecallen import images as ecimg

section_data_set_id = 531477700
all_image_ids = ecimg.get_all_section_image_ids(section_data_set_id)

print("Section Image IDs: ", all_image_ids)

Section Image IDs:  [531477716, 531477704, 531477708, 531477806, 531477718, 531477765, 531477843, 531477755, 531477787, 531477853, 531477789, 531477733, 531477739]

```


### Image Download

```Python

""" Use ecallen.image to download a single image """

from ecallen import images as ecimg

# save a single section image
section_image_id = 531477777
ecimg.save_section_image(section_image_id, 'sample_image.jpg')

# load the saved file and plot
from skimage.io import imread
img = imread('sample_image.jpg')
fig = plt.figure()
ax = fig.add_subplot(1,1,1)
ax.imshow(img)
plt.show()

```

<img
    src="https://i1.wp.com/efferencecopy.net/wp-content/uploads/2017/01/section_image_example.png?resize=768%2C576&ssl=1"
    alt=""
    style="width:425px;margin:0px auto;display:block;"
/>
<figurecaption>
*__Figure 2:__ A raw image from the Allen Brain Atlas. image_ID=531477777.*
</figurecaption></br></br>

### Retrieve image metadata

```Python
""" Use ecallen to retrieve image metadata """

from ecallen import images as ecimg

section_data_set_id = 531477700
params = ecimg.get_imaging_params(section_data_set_id)

params

{'blue_channel': 'tdTomato',
 'genotype': 'Npr3-IRES2-Cre;Ai14-231439',
 'green_channel': 'tdTomato',
 'is_FISH': False,
 'is_ISH': True,
 'plane_of_section': 'coronal',
 'red_channel': 'tdTomato',
 'section_thickness': 25.0}
```

### Neuron segmentation

`ecallen` accomplishes neuron segmentation by applying Otsu's threshold to each image. The routines can deal flexibly with ISH and FISH images.

```Python
""" Use ecallen.images to identify neurons in raw images """

from ecallen import images as ecimg
import numpy as np

# open a raw image
img_path = '/path/to/image/datasetID_167643437_imageID_167643532.jpg'
section_dataset_id = 167643437

# grab the RNA probes from the imaging parameters
img_params = ecimg.get_imaging_params(section_dataset_id)

# run the segmentation function
rprops = ecimg.extract_region_props(img_path,
                                    section_dataset_id,
                                    img_params['probes'],
                                    )

# grab the X and Y pixels at the center of each labeled region
cell_x_pix = np.array([roi['centroid'][1] for roi in rprops])
cell_y_pix = np.array([roi['centroid'][0] for roi in rprops])

```

<img
    src="https://i0.wp.com/efferencecopy.net/wp-content/uploads/2017/02/post_4_figure_9_full_fig.jpg?w=1170&ssl=1"
    alt=""
    style="width:500px;margin:0px auto;display:block;"
/>
<figurecaption>
*__Figure 3:__ ecallen accomplishes automated segmentation of neuron cell bodies from ISH or FISH imaes. Left: Segmented neurons in the neocortex of a single brain image. Each colored spot is a single neuron. Right: a close up of segmented regions superimposed on the raw image.*
</figurecaption></br></br>

### Fast 3D registration

`ecallen` registration routines transform each neurons' location from X,Y space into real "brain space" coordinates. `ecallen` accomplishes fast image registration by implementing affine transformations locally instead of relying on point-by-point registration via the Allen's API. Thus, thousands of points can be registered in seconds, enabling large scale analyses.

```Python
""" Fast 3D registration with ecallen.images """

# define the image and brain IDs
section_dataset_id = 167643437
section_image_id = 308604373

# use ecallen for registration to PIR coordinates
pir = ecimg.xy_to_pir(cell_x_pix,
                      cell_y_pix,
                      section_dataset_id,
                      section_image_id
                      )

```

<img
    src="https://i0.wp.com/efferencecopy.net/wp-content/uploads/2017/04/post_5_fig_3_new_axis.jpg?w=1170&ssl=1"
    alt=""
    style="width:700px;margin:0px auto;display:block;"
/>
<figurecaption>
*__Figure 4:__ ecallen can register the location of points in raw images to the corresponding points in the Common Coordinate Framework. Left: A single raw image with three points in X,Y space. Right: The corresponding points after registration to the Common Coordinate Framework.*
</figurecaption></br></br>

<img
    src="https://i1.wp.com/efferencecopy.net/wp-content/uploads/2017/04/post_5_cover_image_new.jpg?w=325&ssl=1"
    alt=""
    style="width:325px;margin:0px auto;display:block;"
/>
<figurecaption>
*__Figure 5:__ ecallen provides fast image registration for large scale analyses. Here, roughly 124,000 neurons from 44 images have been registered to the common coordinate framework. Each dot is a single neuron and different colors indicate different raw images. In this case, raw images were coronal brain slices. ecallen's registration routines transform each neurons' location from X,Y space into real "brain space" coordinates. *
</figurecaption></br></br>

**[Back to top](#table-of-contents)**


## Contributing

Contributions welcome, just submit an in issue or a pull request!

**[Back to top](#table-of-contents)**

## License

#### (The GNU Version 3 License)

Copyright (C) 2017 Charlie Hass

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program.  If not, see <http://www.gnu.org/licenses/>.

**[Back to top](#table-of-contents)**
