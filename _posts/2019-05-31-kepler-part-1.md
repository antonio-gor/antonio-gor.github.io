---
layout: post
title:  "Kepler: Part 1 - Data Acquisition"
author: Antonio Gordillo Toledo
date:   2019-05-31

---
This is Part 1 of a series in which we work with data from the Kepler Space Telescope. Here, we first learn how to select our target data from the [NASA Exoplanet Archive](https://exoplanetarchive.ipac.caltech.edu/index.html) and then create a script to download the corresponding light curves from the [Mikulski Archive for Space Telescopes](https://archive.stsci.edu/) (MAST). 

Our ultimate goal is to process the data and create a machine learning model to detect potential exoplanets among the dataset. If you would like some background information as to what exoplanets and light curves are, I highly suggest reading Andrew Vanderburg's [tutorial](https://www.cfa.harvard.edu/~avanderb/tutorial/tutorial.html) where he provides both great explanations and animations relating to exoplanets and light curves.

### Environment 
If you wish to follow along, make sure you have python installed on your system. I will be working with python 3.6 on macOS 10.14.

## Data Acquisition
### Exoplanet Archive
We will work with the [Kepler Q1-Q17 DR24 TCE dataset](https://exoplanetarchive.ipac.caltech.edu/cgi-bin/TblView/nph-tblView?app=ExoTbls&config=q1_q17_dr24_tce) in particular because of its completeness and already existing human labels. A <mark>TCE is a threshold crossing event</mark>; these are objects that the Kepler pipeline flagged as being of interest in regards to possible exoplanets.

Once there, we are faced with a wall of rows and columns. Go to 'Select Columns' and <mark>enable 'Autovetter Training Set Label'</mark>, found under 'Autovetter Parameters'. Click on 'Update'. A new column titled 'av_training_set' should appear at the end. By clicking and dragging the column name, move it so that it is the second column, ahead of 'KepID'.

{% include figure image_path="/assets/images/kepler/exoplanet-archive-ss01.png" alt="Exoplanet Archive" caption="Enable 'Autovetter Training Set Label'" %}{: .w-100 .tc}

For more detail about how the training set labels are constructed, see [Autovetter Planet Candidate Catalog for Q1-Q17 Data Release 24](https://exoplanetarchive.ipac.caltech.edu/docs/KSCI-19091-001.pdf).

There are four types of labels:
* PC  : Planetary Candidate
* AFP : Astrophysical False Positive
* NTP : Non-Transiting Phenomenon
* UNK : Unknown

Now would be a good time to look over the other columns to familiarize yourself with the available data.

Ensure that all rows are checked and go to 'Download Table'. If not already selected, select 'CSV Format' and <mark>click on the 'Download Table' button</mark> from the dropdown menu. Rename the CSV file to <mark>'dr24_tce.csv'</mark> and store it in the directory you will be working in. In my case, my working directory will simply be named 'kepler'.

{% include figure image_path="/assets/images/kepler/exoplanet-archive-ss02.png" alt="Download Table" caption="Click 'Download Table'" %}{: .w-100 .tc}

### MAST
Now that we have our CSV file containing our target objects, we can download their corresponding light curves from MAST.

Note: Doing so for all TCEs will require about 87 Gbs of space. You could repeat the steps above to acquire a smaller subset or remove objects from the downloaded CSV file.
{: .alert .alert--danger}

The best option for downloading the light curves is a wget script that reads the CSV file and retrieves the light curves. You can read more about creating your own script [here](https://archive.stsci.edu/kepler/download_options.html). So as to not distract from working with the data itself, we will refer to a python script that generates the wget scripts. This one was written by Chris Shallue. You can get the script, <mark>generate_download_script.py</mark>, from his GitHub [here](https://github.com/google-research/exoplanet-ml/blob/master/exoplanet-ml/astronet/data/generate_download_script.py).

To run the script, <mark>open Terminal and navigate to your kepler directory</mark>. You can learn more about using the terminal [here](https://www.vikingcodeschool.com/web-development-basics/a-command-line-crash-course). My directory is located at '/Users/antonio/kepler', so I will navigate there using the cd command. We then use pwd and ls to verify that our files are there. Then, we run the scripts. Substitute my directory for yours wherever you see mine.

{% highlight shell %}
cd /Users/antonio/kepler
{% endhighlight %}

{% highlight shell %}
pwd
/Users/antonio/kepler
{% endhighlight %}

{% highlight shell %}
ls
dr24_tce.csv			generate_download_script.py
{% endhighlight %}

{% highlight shell %}
python generate_download_script.py --kepler_csv_file=dr24_tce.csv --download_dir=data
12669 Kepler targets will be downloaded to get_kepler.sh
To start download, run:
  ./get_kepler.sh
{% endhighlight %}

{% highlight shell %}
./get_kepler.sh
Downloading 12669 Kepler targets to data
{% endhighlight %}

Once the script has finished downloading the light curves, there should be a new folder containing all the TCE's light curves. That folder contains folders with four-digit names, since <mark>the TCEs have been grouped by the first four-digits of their KepID</mark>. For example, TCE 005364071 can be found in '/data/0053/005364071'. For every TCE there are about 15 to 18 FITS files, which contain the light curves.

{% include figure image_path="/assets/images/kepler/finder-ss01.png" alt="FITS Files" caption="FITS Files for KepID 005364071" %}{: .w-100 .tc}

## Summary
We now have a working directory that contains a CSV file with all the information about each TCE, two scripts used to download the light curves, and a folder which contains all of the corresponding light curves.

{% include figure image_path="/assets/images/kepler/finder-ss02.png" alt="Working Directory" caption="Working Directory" %}{: .w-100 .tc}

Those two scripts are no longer needed and can be removed. I will create a new folder called 'scripts' and move them there.

We went over how to select our target objects from the NASA Exoplanet Archive and how to use scripts to download the corresponding light curves as FITS files from MAST. Now that we have this data, <mark>we can begin analyzing and processing it</mark>. That is the topic of the next parts of this series.
