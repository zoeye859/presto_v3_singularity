# Single Pulse Search Example 

**Under development**


```
> singularity shell PSRSOFT_yhy1.1.simg
singularity> export ASTROSOFT=/PSRSOFT # path environment
singularity> export OSTYPE=linux
singularity> export PGPLOT_DIR=$ASTROSOFT/pgplot
singularity> export PGPLOT_DEV=/xw
singularity> export TEMPO=$ASTROSOFT/tempo
singularity> export TEMPO2=/usr/local/tempo2
singularity> export PRESTO=$ASTROSOFT/presto
singularity> export PSRCAT_RUNDIR=$ASTROSOFT/psrcat_tar
singularity> export PSRCAT_FILE=$ASTROSOFT/psrcat_tar/psrcat.db
singularity> export PATH=/sigproc/bin:$PATH
singularity> export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/lib:/usr/lib64:$PGPLOT_DIR:$ASTROSOFT/lib:$PRESTO/lib:$ASTROSOFT/fftw-3.3.8/lib:$ASTROSOFT/cfitsio-3.48/lib:
singularity> export PATH=$PATH:$ASTROSOFT/bin:$PRESTO/bin:$PGPLOT_DIR
singularity> readfile FILENAME # a list of info of the FILE
singularity> rfifind -time 1.0 -o FILENAME # RFI identification
singularity> prepdata -nobary -o FILE_DM10.0 -dm 10.0 -mask FILE.mask FILENAME  #you don't have to use the mask, and you can change DM according to your search
singularity> prepdata -nobary -o FILE_DM20.0 -dm 20.0 -mask FILE.mask FILENAME 
singularity> single_pulse_search.py --nobadblocks *.dat 
singularity> evince *.ps # look at the search results
singularity> dspsr -S 10 -T 2 -c 2 FILENAME #Chop a short time segment out of the data using dspsr: -s start time -T total time -c period
singularity> pazi FILE #dspsr gives you a substracted file, and pazi plots figures such as the frequency-phase figure

```

