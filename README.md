# LASR
This is a simplified, open-access demo version of the Jcube file system as well as the programs transitfitter, and LASR. These programs were created by the University of Idaho Physics Department. The purpose of this posted code is to test the LASR algorithm. 

The install.sh and Makefiles of the respective programs are tailored to work on Ubuntu 16.04 (x64).

INSTALL DEPENDENCIES
=====================================
The majority of the programs' dependencies install together. However, two libraries need to be installed before installing Jcube:
	
	cspice
		-install from source code (https://naif.jpl.nasa.gov/naif/toolkit.html)
		-after installation go to .../cspice/lib/ and copy cpsice.a into /usr/lib/libcspice.a
   
	cfitsio
		-download from source code (https://heasarc.gsfc.nasa.gov/docs/software/fitsio/fitsio.html)
		-unzip and configure in /usr/include

INSTALL JCUBE, TRANSITFITTER, LASR:

in the JCUBE_2018_LIGHTCURVE_ANLS directory,

	>chmod +x install.sh
	>./install.sh
		
This script installs the libraries qt4, boost, gdal, and commandl. It then installs (in order) Jcube (with the Makefile in the c++/Jcube directory), transitfitter dependencies (in the c++/Jcube/theoreticaltransit directory), transitfitter (in the c++/Jcube/theoreticaltransit/transitfitter directory), and LASR (in the c++/Jcube/LASR directory).

LASR USAGE
=====================================
After installation, LASR runs via command line
	
	>LASR
Settings are controlled in the LASRdata.in file in the c++/Jcube/LASR directory. This file must be in whatever working directory you run LASR out of. In general, LASR reads in and outputs Jcube files, a file system developed by Jason Barnes at the University of Idaho.

Everything in the LASRdata.in file must be tab delineated. Below are usage instructions for this file:

METHOD
-the methods are GEN (generate data), PREP (prepare Kepler time series for fitting, including masking transits -- not pertinent in demo version), PER (calculate Lomb-Scargle normalized periodogram), LASR (run the LASR fitting algorithm), and ANLZ (additional options for analyzing results).

GEN
-used to generate synthetic data to test LASR routine
tseriesfile -> name of output time series (must end in Jcube)
tstr-> start time of time series in seconds
tend -> end time
tspc -> cadence of time bins
st_dev -> 1-sigma Gaussian uncertainty of flux measurements
lag_cor -> lag-1 autocorrelation value between points (scaled 0 to 1)
DATA GAPS -> start and stop times of any data gaps you want in the time series
ADDED OSCILLATIONS-> normalization constant of time series, sinusouids to be added to time series

PREP
-used to prepare Kepler time series for fitting, including masking transits -- not pertinent in demo version

PER
-generate periodogram Jcube for visual purposes & identifying oscillation peaks
tseriesname -> input time series 
periodoname -> name of output periodogram
fstrt -> lowest frequency in Hz for periodogram
fend -> highest frequency in Hz for periodogram
fspc -> spacing between frequency samples

LASR
-runs the Linear Algorithm for Significance Reduction
nsteps -> number of downhill steps to take 
ints -> input time series
outts -> output time series with fitted sinusoid subtracted (to be used in next fit)
outpgm -> output periodogram window after significance reduction (view how much the peak shrank by)
fscale -> starting downhill step size for frequency in Hz
ascale -> starting downhill step size for sinusoid amplitude
pscale -> starting downhill step size for sinusoid phase
npts -> 25
values -> starting guesses for frequency, amplitude, and phase (to remove multiple oscillations simultaneously, list vertically)

ANALYSIS
-ANLZ contains several modes that can be chosen from on the ANALYSIS(UNCER,SUB,WRITE,SORT,PROP) line
-the modes are UNCER (calculate uncertainties of best-fit values), SUB (perform a complete oscillation subtraction using all best-fit values), WRITE (write periodogram or time series Jcube files as .txt files for easy use outside of these programs), SORT (sort fitfreqs Jcube by ascending frequency), PROP (propagate best-fit uncertainties into the subtracted time series), ADDT (add periodic transit events to synthetic data using a template transit)

TRANSITFITTER USAGE
=====================================
I also included the program transitfitter as part of this code to make visualization of Jcube time series and periodograms easier. Transitfitter is a complicated program -- I do not include its full functionality in this demo and I only list its usage abilities and options that are directly relevant to LASR.

To open transitfitter, run:
	>transitfitter
To open transitfitter with a specific file, run:
	>transitfitter "filename.Jcube"

Transitfitter is designed to view transit light curves; it can also display periodograms but the default scales and options are not as well suited for frequency-space.

ZOOM IN ON DATASET
-the button that "zooms in" on a region of a dataset is the 'Crop' button to the right of the display 
-the center of the zoom will be whatever value is listed in Time at Center (s) below the display
-the width of the zoom is controlled by To Crop: (right), which scales with the Period(d) (below)

VIEW FITTING RESULTS
-with demotimeseries.Jcube loaded into transitfitter, you can view the fitting results of LASR
-using the drop-down options menus above the display, go to Modify->Oscillation Subtract
-in the new window that pops up, click Load Freqs and select the demofitfreqs.Jcube file
-click Fit!
	--this does not perform an actual fit in the demo mode. I set all parameters to be static, so all this does is display a best-fit line
-to the right of the display, the button 'Residual' will display the residual of the best-fit

CREATING/STORING FITFREQS.JCUBE
=====================================
For the purposes of this demo, I have added two simple additional bits of code to make handling fitfreqs Jcube files easier -- out2fitfreqs and fitfreqs2out. To use one these commands, first go into the c++/Jcube/LASR Makefile and edit the top line to say the name of the program you want to compile. In that directory, run:
	>make
(do the same 2 steps for both programs).

With out2fitfreqs and fitfreqs2out compiled, you can convert a fitfreqs Jcube file to ASCII and back again. Example usage:
	>fitfreqs2out fitfreqs.Jcube fitfreqs.out
	>fitfreqs2out fitfreqs.out fitfreqs.Jcube
I have included both a demofitfreqs.Jcube file and a demofitfreqs.out file. The format of fitfreqs.out is straightforward, but must match the example with 3 tab-delineated 0's at the end of each line.

DEMO FILES INCLUDED
=====================================
The c++/Jcube/LASR directory includes several demo files to show how LASR works.
demotimeseries.Jcube -> time series with 7 oscillations, data gaps, a small injected transit, and correlated noise
demoperidogram.Jcube -> Lomb-Scargle normalized periodogram of demotimeseries.Jcube
demofitfreqs.Jcube -> demo results file of best-fits of the demotimeseries.Jcube using LASR
demofitfreqs.out -> ASCII version of demofitfreqs.Jcube
