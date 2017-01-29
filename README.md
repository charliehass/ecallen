# ecallen
Python package for easy interaction with the Allen Institute for Brain Science. Includes routines to download images and provides fast 3D registration to the Common Coordinate Framework.

Please visit [efferencecopy.net](https://efferencecopy.net/ecallen-a-python-package-for-the-allen-institutes-api/) for a full explanation of the package's functionality including detailed examples.

## Table of Contents

1. [Installation](##Installation)
1. [Usage](##Usage)
    1. [Retrieve image IDs](###Retrieve image IDs)
    1. [Image Metadata](###Retrieve image metadata)
    1. [Image Download](###Image Download)
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

For a complete discription of this package visit [efferencecopy.net](https://efferencecopy.net/allen-brain-atlas-interfacing-with-the-allen-api/). The following example illustrate basic API queries and image downloads.

### Retrieve image IDs

```Python

""" Use ecallen to retrieve all image_id for an experiment """

section_data_set_id = 531477700
all_image_ids = ecimg.get_all_section_image_ids(section_data_set_id)

print("Section Image IDs: ", all_image_ids)

Section Image IDs:  [531477716, 531477704, 531477708, 531477806, 531477718, 531477765, 531477843, 531477755, 531477787, 531477853, 531477789, 531477733, 531477739, 531477800, 531477841, 531477702, 531477722, 531477767, 531477796, 531477804, 531477798, 531477829, 531477743, 531477714, 531477712, 531477735, 531477779, 531477818, 531477763, 531477855, 531477825, 531477785, 531477759, 531477794, 531477737, 531477845, 531477808, 531477832, 531477706, 531477747, 531477729, 531477810, 531477835, 531477749, 531477777, 531477816, 531477757, 531477727, 531477745, 531477849]

```


### Retrieve image metadata

```Python
""" Use ecallen to retrieve image metadata """

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

### Image Download

```Python

""" Use ecallen.image to download a single image """

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
![](https://github.com/efferencecopy/ecallen/blob/master/resources/example_brain_img.png "example brain image")


**[Back to top](#table-of-contents)**


## Contributing

Contributions welcome, just submit a in issue or a pull request!

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
