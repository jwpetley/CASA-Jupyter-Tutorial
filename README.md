# CASA-Jupyter-Tutorial
This repository contains data and a jupyter notebook to process it using a CASA jupyter notebook kernel. The tutorial is mainly intended to demonstrate the capability of CASA in notebook form and so if you would like to skip this VLBI setup stage then feel free to use your own measurement set. Once you have a measurement set (.ms directory) then simply open up the tutorial.ipynb with the CASA kernal to get going. Alternatively you can create the measurement set within the Jupyter notebook (the introduction of the notebook shows how to do this and contains all the scripts) although I have found that this is a tad slower. If you would like to make your measurement set in advance the rest of the README is devoted to explaining how to do this but it is not necessary.



This tutorial uses publicly available data from the EVN archive (http://www.jive.nl/select-experiment). Here you can search by experiment number, PI or object. If you would like to follow along with this tutorial then please use the attached experiment 'EC067C'. Otherwise you are free to use your own data from EVN.

What is needed are the FITS IDI files from the fits tab, and the antab file and uvflag file from the pipeline tab. You should also clone the casa-vlbi repository (https://github.com/jive-vlbi/casa-vlbi) as the scripts will be needed to modify this data ready for it to be turned into a CASA measurement set.

---

<h2> Use this if you are making the MS outside of the notebook </h2>

This README will teach you how to access and setup data ready to be processed using a CASA jupyter notebook. To find out how to set up the notebook system please refer to https://github.com/aardk/jupyter-casa which has an in depth tutorial. The easiest system to set this up is using Docker.

<h2> Initial Data Processing </h2>

Translating the flagging to a format CASA can read is straightforward:

`python <path_to_casa-vlbi_scripts>/flag.py ec067c.uvflg ec067c_1_1.IDI1 > ec067c.flag`

Flagging will be the same for all IDI files so you only need to bother with the first,

Now we have to append the Tsys values from the ANTAB file to the FITS-IDI files which we will read into CASA. Make sure to only run this once otherwise it will append twice and cause issues. Be patient, this may take a few minutes depending on the size of your experiment.

`casa --nogui -c <path_to_casa-vlbi_scripts>/append_tsys.py ec067c.antab ec067c_1_1.IDI*`

We generate the gain curve calibration (and dpfu) from the Antab file using the script gc.py:

`casa --nogui -c $PYCAPATH/gc.py n14c3.antab EVN.gc -l 1 -u 90`

The -l and -u options set lower and upper elevation limits in degrees. Without them you can run into square root errors so experiment with them to obtain what you need.

<h3> Creating the MS </h3>

Finally we need to create the measurement set. I have tried to do this within the Jupyter notebook but have been running into issues I cannot seem to fix. Instead I would recommend doing this in the normal interactive CASA way.


```
$ casa   #This will initialise the interactive CASA environment

...Something about IPython
...Something about CASA
...Something about logging

 <1>  import glob
importfitsidi(vis='ec067c.ms', fitsidifile=sorted(glob.glob('ec067c*IDI?')),
              constobsid=True, scanreindexgap_s=15.0)
  ```
  Glob is used to make file finding much easier. This process may also take a few minutes but at least it will tell you percentage completion in the terminal in which you are running the notebook!
