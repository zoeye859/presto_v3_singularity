# PRESTO 3.0.1 SINGULARITY CONTAINER

## HOW TO DOWNLOAD THE CONTAINER

Download it from my personal website

```
wget http://haoyangye.com/presto_yhy.simg
```

Want to know more about singularity container and singularity instructions, see *ReadMore.md* under the same github repo.

You can issue a problem here or contact me *HAOYANG.YE AT CANTAB.NET*


## HOW TO USE THE CONTAINER

Download presto_yhy.simg 

Put it somewhere on your computer.

Create a presto_simg.sh as below:

```
#!/bin/bash
export ASTROSOFT=/PSRSOFT

# OSTYPE
export OSTYPE=linux

# PGPLOT
export PGPLOT_DIR=$ASTROSOFT/pgplot
export PGPLOT_DEV=/xw

#TEMPO
export TEMPO=$ASTROSOFT/tempo

# PRESTO
export PRESTO=$ASTROSOFT/presto

# LD_LIBRARY_PATH
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/lib:/usr/lib64:$PGPLOT_DIR:$ASTROSOFT/lib:$PRESTO/lib:$ASTROSOFT/fftw-3.3.8/lib:$ASTROSOFT/cfitsio-3.48/lib:
# PATH
# Some Presto executables match sigproc executables so keep separate -
# all other executables are found in $ASTROSOFT/bin
export PATH=$PATH:$ASTROSOFT/bin:$PRESTO/bin:$PGPLOT_DIR

### things you want presto to do


readfile GBT_Lband_PSR.fil
rfifind -time 1.0 -o Lband GBT_Lband_PSR.fil
exploredat Lband_topo_DM0.00.dat
DDplan.py -d 500.0 -n 96 -b 96 -t 0.000072 -f 1400.0 -s 32 -r 0.5
```

You may want to change the last several lines, to inform presto the location of the file GBT_Lband_PSR.fil

If you want to obtain this file for test, download it by
```
wget http://www.cv.nrao.edu/~sransom/GBT_Lband_PSR.fil
```

Then exec the singularity image file as:

```
singularity exec presto_yhy.simg sh presto_simg.sh
```

You can add more commands to use presto by appending the .sh file. 

For more presto tutorial, tune in https://www.cv.nrao.edu/~sransom/PRESTO_search_tutorial.pdf


## MORE ABOUT THE CONTAINER

If you want to find where the Presto folder is, please go to /PSRSOFT.

You can also use the container as an ubuntu environment.

The Singularity command you need is:

```
$ singularity shell presto_yhy.simg

```


