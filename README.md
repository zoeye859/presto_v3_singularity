# PRESTO 3.0.1 SINGULARITY CONTAINER

It includes almost the full suite of Pulsar/FRB signal processing software: 

- PRESTO
- DSPSR 
- SIGPROC
- PSRCHIVE

System:

- Linux
- Mac OS (see https://github.com/zoeye859/presto_v3_singularity/blob/master/Solution_to_MacXserver_problem.md)

## Version

Currently, I have the following versions of the container:

- presto_yhy.simg (PRESTO V3 only)
- PSRSOFT_yhy1.0.simg (add DSPSR, SIGPROC and PSRCHIVE) and PSRSOFT_yhy1.0.img (for Windows users)
- PSRSOFT_yhy1.1.simg (reinstalled tempo2 to solve the reported issue #3 )

The **PSRSOFT_yhy1.1.simg** is recommended (updated on 17 Sept 2020). 

## HOW TO DOWNLOAD THE CONTAINER

Download it from my personal website

```
wget http://haoyangye.com/PSRSOFT_yhy.simg
```

Want to know more about singularity container and singularity instructions, see *ReadMore.md* under the same github repo.

You can issue a problem here or contact me *HAOYANG.YE AT CANTAB.NET*

If you find the **download is too slow**

- Users in China: Baidu drive:https://pan.baidu.com/s/1LVFsvPhX1ipcAnni0xO79A  password:j4yb
- Or you can contact me (*HAOYANG.YE AT CANTAB.NET*) to arrange a **Wetransfer** job


## HOW TO USE THE CONTAINER

Download PSRSOFT_yhy.simg 

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

#TEMPO2
export TEMPO2=/usr/local/tempo2

# PRESTO
export PRESTO=$ASTROSOFT/presto

# PSRCAT
export PSRCAT_RUNDIR=$ASTROSOFT/psrcat_tar
export PSRCAT_FILE=$ASTROSOFT/psrcat_tar/psrcat.db

# DSPSR
export PATH=/sigproc/bin:$PATH

# LD_LIBRARY_PATH
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/lib:/usr/lib64:$PGPLOT_DIR:$ASTROSOFT/lib:$PRESTO/lib:$ASTROSOFT/fftw-3.3.8/lib:$ASTROSOFT/cfitsio-3.48/lib:
# PATH
# Some Presto executables match sigproc executables so keep separate -
# all other executables are found in $ASTROSOFT/bin
export PATH=$PATH:$ASTROSOFT/bin:$PRESTO/bin:$PGPLOT_DIR



### things you want to do 


readfile GBT_Lband_PSR.fil
rfifind -time 1.0 -o Lband GBT_Lband_PSR.fil
prepdata -nobary -o Lband_topo_DM0.00 -dm 0.0 -mask Lband_rfifind.mask -numout 530000 GBT_Lband_PSR.fil
DDplan.py -d 500.0 -n 96 -b 96 -t 0.000072 -f 1400.0 -s 32 -r 0.5

```

You may want to change the last several lines, to inform presto the location of the file GBT_Lband_PSR.fil, and add your own commands.


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


