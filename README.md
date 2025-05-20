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
