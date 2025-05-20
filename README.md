# hysplit-saws-chpc
HYSPLIT is a NOAA model used to simulate the transport and dispersion of airborne particles and pollutants using meteorological data.

✅ What this repository aims to do:
- Download the source code and data files from your /home or Lustre directory.
- Compiling HYSPLIT from source using Intel or GNU compilers (Lengau has both).
- Running HYSPLIT via interactive jobs or batch scripts (using Slurm).

What is available for downloading
Currently several LINUX distributions (executables) are available as zipped tar files to "registered" HYSPLIT users with more "flavors" being added in the near future.

Pre-Compiled Distributions:
- Ubuntu 20.04
- Red Hat Enterprise Linux 8 / CentOS 8
- All Linux distributions (experimental)

## Prerequisites:
Before installation, ensure the following software is installed on your system:
- Tcl/Tk (version 8.5 or later) – required for the graphical user interface (GUI).
- ImageMagick – for converting graphics formats.
- Ghostscript – to view Postscript graphics.
- Firefox – the GUI and tutorial scripts search for its installation location.
- SVN or WGET – needed to download the source code.
- gfortran and gcc compilers – required for compiling the source code.
- NetCDF library – necessary if you're converting WRF-ARW data fields.

This tutorial outlines two primary methods: **installing a pre-compiled binary** or compiling the source code yourself. The focus of this tutorial will be on the former rather than the latter.

---

With pre-compiled binaries, you do not need to build HYSPLIT, the compilation has already been done for you. Instead, simply set up the environment and run the provided executables. 

## 1. Locate the Executables

Look for a directory named ```exec/``` (or sometimes ```bin/```) inside your extracted HYSPLIT directory. It should contain files like:
- ```hyts_std```
- ```hycs_std```
- ```con2stn```
- ```trajplot```
- etc.

List them with:
```bash
ls hysplit.v5.4.2_x86_64/exec/
```

## 2. Set Up the Environment

You may need to set some environment variables so HYSPLIT can find its data and run correctly. This is usually described in a ```README``` or ```INSTALL``` file in the package.
Variables to set:
```bash
export HYSPLIT_DIR=/path/to/hysplit.v5.4.2_x86_64
export PATH=$HYSPLIT_DIR/exec:$PATH
```
Replace /path/to/hysplit.v5.4.2_x86_64 with the actual path to your extracted directory.

## 3. Test the installation
Try running one of the executables
```bash
hyts_std
# OR
$HYSPLIT_DIR/exec/hyts_std
```
## 4. Run Example Jobs
There are usually example input files and scripts in the package (often in an ```example/``` or ```working/``` directory). You can try running an example job to verify everything works.

Summary:
- No build step is needed.
- Set environment variables.
- Add the exec/ directory to your PATH.
- Run the provided executables.

## 5. PBS Batch Script Example
Save this as ```hysplit_test.pbs``` (or similar):
```bash
#!/bin/bash
#PBS -N hysplit_test
#PBS -l select=1:ncpus=1:mem=2gb
#PBS -l walltime=01:00:00
#PBS -j oe
#PBS -o hysplit_test.log
#PBS -m abe
#PBS -M your.email@domain.com

# Load any required modules (if needed)
# module load intel/2020u1
# module load chpc/earth/netcdf/4.7.4/intel2020u1

# Set up environment variables
export HYSPLIT_DIR=/path/to/hysplit.v5.4.2_x86_64
export PATH=$HYSPLIT_DIR/exec:$PATH

# Set working directory to where you submitted the job
cd $PBS_O_WORKDIR

# Set model variables
MDL="$HYSPLIT_DIR"
OUT="."
MET="/work/data"

poll="IND"
syr=98
smo=05
shr=00

olat=27.00
olon=72.05
olvl=10.0

run=840
ztop=10000.0
met1="fnl.nh.may98.001"
met2="fnl.nh.may98.002"
met3="fnl.nh.jun98.001"

for sda in 12 13 14 15; do
  cat > CONTROL <<EOF
$syr $smo $sda $shr    
1                      
$olat $olon $olvl      
$run                   
0                      
$ztop                  
3                      
$MET/                  
$met1                  
$MET/                  
$met2                  
$MET/                  
$met3                  
1                      
$poll                  
10.0                   
0.1                    
$syr $smo $sda $shr 00 
1                      
30.0 70.0              
0.50 0.50              
20.0 20.0              
$OUT/                  
cdump                  
1                      
10                     
00 00 00 00 00         
00 00 00 00 00         
00 24 00               
1                      
0.0 0.0 0.0            
0.0 0.0 0.0 0.0 0.0    
0.0 0.0 0.0            
5.27                   
0.0                    
EOF

  # Run the model
  [ -f cdump ] && rm cdump
  $MDL/exec/hycs_std
  mv cdump ${poll}${smo}${sda}

  run=$((run-24))
done

```
Edit the script:
- Set HYSPLIT_DIR to the path where you extracted the HYSPLIT binaries.
- Set MET to the path where your meteorological data files are located.
- Set your email address for PBS notifications.
