##########################
Standard ROMS installation
##########################

Complete the instructions for downloading ROMS from the `README in the MyROMS
repository <https://github.com/myroms/roms>`_ and then follow the `Getting
started guide at myroms.org <https://www.myroms.org/wiki/Getting_Started>`_.

Install Git Large File Storage
==============================

`Git Large File Storage (LFS) <https://git-lfs.com/>`_ "replaces large files
such as audio samples, videos, datasets, and graphics with text pointers inside
Git, while storing the file contents on a remote server like GitHub.com or
GitHub Enterprise."

ROMS uses git lfs to manage files necessary to run the test cases in
`roms_test <https://github.com/myroms/roms_test>`_.

.. code-block::

   su $ADMIN
   sudo port install git-lfs
   exit
   vim ~/.gitconfig
   # Append this to the end of .gitconfig
   [filter "lfs"]
        clean = git-lfs clean -- %f
        smudge = git-lfs smudge -- %f
        process = git-lfs filter-process
        required = true
   :wq



Download ROMS and set environmental variables
=============================================

.. code-block::

   git clone https://github.com/myroms/roms.git
   git clone https://github.com/myroms/roms_test.git
   git clone https://github.com/myroms/roms_matlab.git

   vim ~/.bashrc
   # ROMS
   export ROMS_ROOT_DIR='/Users/$USER/work/git'

   :wq

   source ~/.bashrc



Upwelling application
=====================

.. note::

   Cases in ROMS are known as "applications" and are defined in the
   ``ROMS_APPLICATION`` environmental variable which is set in
   ``roms/ROMS/Bin/build_roms.sh``:
   
   .. code-block::
      
      export   ROMS_APPLICATION=UPWELLING

      # Set the header directory to the one included in the ROMS distro
      export     MY_HEADER_DIR=${MY_ROMS_SRC}/ROMS/Include

      # Set FORT to the compiler used on your system
      export              FORT=gfortran

When trying to run the standard build script, if an error similar to the
following is thrown:

.. error::

   .. code-block::

      ROMS/Include/cppdefs.h:729:11: fatal error: '/Users/johnsonb/work/git/roms/ROMS/Bin/upwelling.h' file not found
      # include ROMS_HEADER
               ^~~~~~~~~~~
      <command line>:8:21: note: expanded from macro 'ROMS_HEADER'
      #define ROMS_HEADER "/Users/johnsonb/work/git/roms/ROMS/Bin/upwelling.h"

That means that ``MY_HEADER_DIR`` isn't set in ``build_roms.sh``.

If an error similar to the following is thrown:

.. error::

   .. code-block::

      gfortran-mp-12: error: unrecognized command-line option '-fp-model'; did you mean '-fipa-modref'?
      gfortran-mp-12: error: unrecognized command-line option '-h'
      gfortran-mp-12: error: unrecognized command-line option '-ip'; did you mean '-p'?
      gfortran-mp-12: error: unrecognized command-line option '-traceback'
      gfortran-mp-12: error: unrecognized command-line option '-check'; did you mean '-fcheck='?

That means that ``FORT`` isn't set correctly in ``build_roms.sh``.

Nominally, this will compile a distributed-memory version of ROMS, ``romsM``.

To run the executable:

.. code-block::

   mpirun -np 1 ./romsM ../External/roms_upwelling.in

At runtime, if an error similar to the following is thrown:

.. error::

   ``ERROR: Cannot open file 'ROMS/External/varinfo.yaml': No such file or directory``

That means that the path to ``VARNAME`` isn't set correctly in ``roms_upwelling.in``.

.. code-block::

   vim ../External/roms_upwelling.in
   VARNAME = /Users/$USER/work/git/roms/ROMS/External/varinfo.yaml
   :wq

Successful completion of the integration should produce a set of NetCDF files:

.. code-block::

    ls -1rt
       roms_his.nc # History
       roms_avg.nc # Averages
       roms_dia.nc # Diagnostics
       roms_rst.nc # Restart

WC13 application
================

Overview
--------

WC13 refers to the West Coast 2013 application described by Moore et 
al., (2011) [1]_ used to study the California Current System. The `wc_ini.nc
file stored in the roms_test repository
<https://github.com/myroms/roms_test/blob/main/WC13/Data/wc13_ini.nc>`_
has a start date of January 3rd, 2004 (ocean time has units of seconds since
1968-05-23 00:00:00 and the value of 1123891200).

The standard configuration onf ``roms_wc13.in`` has the number of timesteps
between writing restart records, ``NRST``, set to ``192``. The length of each
timestep, ``DT``, is set to ``1800.0d0`` seconds (30 minutes) thus, with 2
timesteps per hour, the output frequency for restart files is every 4 days.

The initial set of restart files will be produced on January 7th, 2004 after
running the model.

The ``NRREC`` setting indicates whether to restart from the initial file or 
whether to as described in the `MyROMS documentation
<https://www.myroms.org/wiki/Variables#nrrec>`_. If ``NRREC`` is set to -1, 
the model will restart from the most recent restart file.

Modify the ``build_roms.sh`` script to change the application to ``WC13`` and 
then compile.

.. code-block::

   cd roms/ROMS/Bin
   vim build_roms.sh
   export   ROMS_APPLICATION=WC13
   :wq
   ./build_roms.sh

.. code-block::

   vim roms/ROMS/External/roms_wc13.in
   VARNAME = /Users/$USER/work/git/roms/ROMS/External/varinfo.yaml
   :wq

.. note::

   Note that the default tile setting in WC13 is 2 x 2:

   .. code-block::

      vim ../External/roms_wc13.in
      NtileI == 2                               ! I-direction partition         
      NtileJ == 2                               ! J-direction partition
      :wq

Thus, the number of processes should be run as ``-np 4``:

.. code-block::

   mpirun -np 4 ./romsM ../External/roms_wc13.in

At runtime, if this error is thrown:

.. error::

   cannot find input file: wc13_ini.nc

The ``roms_wc13.in`` file must be modified further:

.. code-block::

   vim ../External/roms_wc13.in
   ININAME == ../../../roms_test/WC13/Data/wc13_ini.nc
   :wq

At runtime, if an error similar to this is thrown:

.. error::

   ERROR: Abnormal termination: NetCDF OUTPUT.
   REASON: NetCDF: Unknown file format

Remember to `Install Git Large File Storage`_.

At runtime, if an error similar to this is thrown:

.. error::

   GET_GRID_NF90 - unable to find grid variable: visc_factor
   in grid NetCDF file: wc13_grd.nc

The ``roms_wc13.in`` file can be modified:

.. code-block::

   LuvSponge == F ! horizontal momentum

At runtime, if an error similar to this is thrown:

.. error::

   GET_GRID_NF90 - unable to find grid variable: diff_factor
   in grid NetCDF file: wc13_grd.nc

The ``roms_wc13.in`` file can be modified:

.. code-block::

   LtracerSponge == F F

Updated location
----------------

Since most of the input files are in the ``roms_test/WC13/Data/`` directory,
move the ``romsM`` executable and the ``roms_wc13.in`` file there and run from
that directory:

.. code-block::

   mpirun -np 4 ./romsM ./roms_wc13.in

As noted above, the default configuration of the WC13 application is to
initialize from 0Z January 3rd, 2004 and produce restart files at 0Z January
7th, 2004.

Suitable WOD09 obs_seq files are stored on campaign storage at: 
``/glade/p/cisl/dares/Observations/WOD09/200401/obs_seq.0Z.20040107``

References
==========

.. [1] Moore, Andrew M., Hernan G. Arango, Gregoire Broquet, Brian S. Powell,
       Anthony T. Weaver, Javier Zavala-Garay, 2011: The Regional Ocean
       Modeling System (ROMS) 4-dimensional variational data assimilation
       systems: Part I – System overview and formulation, *Progress in
       Oceanography*, **91**, 34-49, `doi 10.1016/j.pocean.2011.05.004
       <https://doi.org/10.1016/j.pocean.2011.05.004>`_.
