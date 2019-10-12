---
layout: post
title:  "Kepler: Part 2 - Data Processing and EDA"
author: Antonio Gordillo Toledo
date:   2019-06-01

---
This is Part 2 of a series in which we work with data from the Kepler Space Telescope. Here, we learn how to use the Python package Lightkurve to manipulate our data and embark on some exploratory data analysis (EDA).

If you haven't already, read [Kepler: Part 1 - Data Acquisition](/kepler-part-1/).

Before we begin writing any code, let's work out a plan for how we will proceed. We want to

* Open the CSV file and extract the TCE data/parameters
* Open all FITS files and extract the light curve data

## Environment
If you don't already have Python and Jupyter Notebooks, I suggest using the [Anaconda](https://www.anaconda.com/distribution/) distribution of Python. It includes many useful libraries for scientific computing and allows us to easily create environments for our projects. Specifically, I will be using

* Python 3.6.8
* conda 4.6.14
* macOS 10.14.3

We will now set up our environment.

**If you have Anaconda installed**

Open Terminal and navigate to your kepler directory from Part 1. Substitute my filepath for yours.

{% highlight shell %}
cd /Users/antonio/kepler
{% endhighlight %}

Create and activate an environment called kpy

{% highlight shell %}
conda create -n kpy python=3.6
{% endhighlight %}

{% highlight shell %}
conda activate kpy
{% endhighlight %}

Install the packages

{% highlight shell %}
conda install pandas numpy matplotlib jupyter
{% endhighlight %}

{% highlight shell %}
conda install --channel conda-forge lightkurve
{% endhighlight %}

Launch Jupyter Notebooks
{% highlight shell %}
jupyter notebook
{% endhighlight %}

Create a new Python 3 notebook

{% include figure image_path="/assets/images/kepler/jupyter-ss01.png" alt="New Python 3 Notebook" caption="New Python 3 Notebook" %}{: .w-100 .tc}

Rename it to 'kepler_data_processing' and make sure it is running.

{% include figure image_path="/assets/images/kepler/jupyter-ss02.png" alt="Running Notebook" caption="Running Notebook" %}{: .w-100 .tc}

**If you *do not* have Anaconda installed**

You will have to emulate the steps above using your preferred method for environment control and package installation, such as pip. Ensure that you install the same packages, as those will all be used below.

{% highlight shell %}
pip install pandas numpy matplotlib jupyter lightkurve
{% endhighlight %}


## Data Processing
Now that our environment is setup, we can begin with the plan we outlined above. We will create a function for every task so that we have a modular system. This also has the benefit of allowing us to quickly test and edit every part.

We begin by importing the libraries we will use.

{% highlight python %}
## Packages to be used
import matplotlib.pyplot as plt
from random import shuffle
import lightkurve as lk
import pandas as pd
import numpy as np
import glob
import time
import sys
import os
{% endhighlight %}

{% highlight python %}
def open_files(csv_file):
    '''
    The open_files function opens the csv and extracts the needed data.

    Args:
        csv_file: Should contain desired TCEs and parameters.
          Required Parameter: kepid,
                              av_training_set,
                              tce_plnt_num,
                              tce_period,
                              tce_time0bk.

    Returns:
        tce_data: Panda DataFrame that has the parameters listed above.
    '''

    ## Opening csv as tce_info using pandas
    ## This contains all metadata for all TCEs
    tce_info = pd.read_csv(csv_file)

    ## Removing TCEs with label 'UNK'
    tce_info = tce_info[tce_info.av_training_set != 'UNK']#.reset_index()

    ## Isolate AFP and NTP
    not_pc = tce_info[tce_info['av_training_set'] != 'PC']

    ## Isolate all PCs
    pc_only = all_of_it[tce_info['av_training_set'] == 'PC']

    ## Only keep TCE with tce_plnt_num == 1 and reset the index
    pc_only = pc_only[pc_only.tce_plnt_num == 1].reset_index(drop = True)

    ## Add PCs back to full set
    tce_info = pd.concat([pc_only, not_pc], ignore_index=True, sort=False)

    ## Shuffle the dataframe because all PCs are at the start
    tce_info = tce_info.sample(frac=1).reset_index(drop=True)


    ## Extracting and combining kepids, periods, epochs into one DataFrame
    tce_data = tce_info[['kepid',
                         'av_training_set',
                         'tce_plnt_num',
                         'tce_period',
                         'tce_time0bk']]

    return tce_data
{% endhighlight %}

This series is a work in progress. Progress ends here, for now.
