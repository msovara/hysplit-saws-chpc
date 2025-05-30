# 🚀 Running HYSPLIT 5.4.2 on CHPC's Lengau Cluster
## 🌐 Overview
This guide provides step-by-step instructions for setting up and executing HYSPLIT 5.4.2 atmospheric dispersion simulations on the CHPC Lengau HPC cluster using the Intel toolchain.

---

## 📋 Prerequisites
- **🔑 Active account** on Lengau cluster
- **💾 Lustre scratch space** allocation for simulations
- **⌨️ Basic proficiency** with Linux CLI and PBS job submission
- **🏷️ Project ID** for resource accounting

---

## 🛠️ Setup and Execution

### 1. 📦 Load HYSPLIT Module
Initialize the environment:
```bash
module use /apps/chpc/scripts/modules
module load earth/hysplit/5.4.2
```

## 📁 2. Configure Working Directory
You must not run simulations in the central install directory. Instead, copy the working directory to your Lustre scratch space:
```bash
cp -r $HYSPLIT_WORKING ${LUSTRE_SCRATCH}/hysplit_working
cd ${LUSTRE_SCRATCH}/hysplit_working
```
Replace with your actual Lustre scratch path.

## 📝 3. Preparing Input Files

### 3.1 Edit ```CONTROL``` to define:
- 📈 Meteorological data paths
- 💨 Release parameters
- 💾 Output settings

### 3.2 Configure ```SETUP.CFG``` for:
- 🧪 Chemical species
- ⬇️ Deposition options
- 🔍 Grid resolution

### 3.3 Place required meteorological files in paths referenced in ```CONTROL```

## ▶️ 4. Execute Simulations:
**Concentration Simulation**:
```bash
$HYSPLIT_EXEC/hycs_std
```
**Trajectory Simulation**:
```bash
$HYSPLIT_EXEC/hyts_std
```

## 💼 5. Sample PBS Job Script
Example submission script (```hysplit_job.pbs```): 
```bash
#!/bin/bash
#PBS -N HYSPLIT_Sim
#PBS -l walltime=12:00:00
#PBS -l select=1:ncpus=24:mpiprocs=24
#PBS -q normal
#PBS -P <YOUR_PROJECT_ID>  # Replace with actual project ID
#PBS -o stdout.log
#PBS -e stderr.log

# Initialize environment
module purge
module use /apps/chpc/scripts/modules
module load earth/hysplit/5.4.2

# Configure workspace
WORKDIR=${LUSTRE_SCRATCH}/hysplit_working_${PBS_JOBID}
cp -r $HYSPLIT_WORKING ${WORKDIR}
cd ${WORKDIR}

# Runtime configuration (customize before submission)
sed -i "s|/default/path|${WORKDIR}|" CONTROL   # Update paths
# Add other pre-run configurations here

# Execute simulation
$HYSPLIT_EXEC/hycs_std

# Post-processing (example)
echo "Simulation completed at $(date)" >> status.log
```

**Submit with**
```bash
qsub hyspli_job.pbs
```

## 📤 6. Output Files
📄 Primary output files:
- cdump (concentration outputs)
- tdump (trajectory outputs)
- MESSAGE (runtime logs)

🔬 **Post-processing**:
- Use HYSPLIT's ```con2asc``` to convert binary outputs
- Visualize with Python/R scripts or GIS tools
- Analyze with included utilities in ```$HYSPLIT_EXEC```
---

# 🆘 Support Resources
- CHPC Helpdesk: https://users.chpc.ac.za/helpdesk/tickets/submit/
- HYSPLIT Official Documentation: https://www.ready.noaa.gov/HYSPLIT.php
- HYSPLIT GitHub Issues

---

### 💡 Pro Tip: For repeated simulations, create template directories with predefined configurations to accelerate setup. Monitor jobs with ```qstat -u $USER``` and analyze runtime patterns from ```MESSAGE``` files to optimize resource requests.
