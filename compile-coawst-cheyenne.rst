##########################
Compile COAWST on Cheyenne
##########################

These compilation instructions are adapted from the
`set up COAWST model <_static/set-up-COAWST-model.pdf>`_ document provided by
Jose.

Download the source code from the GitHub repository
===================================================

.. code-block::

   cd /glade/work/$USER/git
   git clone https://github.com/jcwarner-usgs/COAWST.git
   cd COAWST
   COAWST_ROOT=$(pwd)
   git checkout -b dart_test
   Switched to a new branch 'dart_test'

Build the MCT libraries
=======================

Use configure to create a makefile for MCT:

.. code-block::

   module list
   Currently Loaded Modules:
   1) ncarenv/1.3    3) ncarcompilers/0.5.0   5) netcdf/4.8.1   7) nco/5.0.3
   2) intel/19.1.1   4) mpt/2.25              6) ncl/6.6.2
   cd $COAWST_ROOT/Lib/MCT
   ./configure
   [ ...  ]
   config.status: creating Makefile.conf
   Please check the Makefile.conf
   Have a nice day!
   make

Copy the compiled mpeu library into the mct directory:

.. code-block::

   cp mpeu/libmpeu.a mct/

Edit files in the compiler directory
====================================

.. code-block::

   cd $COAWST_ROOT/Compilers
   vim Linux-ifort.mk

   227    MCT_INCDIR ?= /glade/work/$USER/git/COAWST/Lib/MCT/mct
   228    MCT_LIBDIR ?= /glade/work/$USER/git/COAWST/Lib/MCT/mct

Copy the build script into the project directory
================================================

For this example, we will be using the ``Inlet test`` project included in
COAWST, since its ``CPPDEFS`` are similar to those used for Jose's Sanibel
Island domain.

.. code-block::

   cd $COAWST_ROOT/Projects/Inlet_test
   cp ../../coawst.bash ./
   
Edit the setup script to declare the location of the COAWST installation
------------------------------------------------------------------------

.. code-block::

   vim coawst.bash
   132 export   MY_ROOT_DIR=/glade/work/johnsonb/git/COAWST

Edit the project's header script to output verification observations
====================================================================

.. note::

   ROMS should be configured to output a data assimilation restart file, named
   the ``ocean_dai.nc`` file in order to provide verification observations for 
   DART. DART refers to the ROMS verification observations as "precomputed
   forward operators." For more information on this capability, read the ROMS
   upgrade ticket description for
   `4D-Var or EnKF initial/restart file, ROMS depths, Observations <https://www.myroms.org/projects/src/ticket/697>`_.

In order to configure ROMS to output the ``ocean_dai.nc`` file, the
CPP-preprocessing ``ENKF_RESTART`` option must be set in the project's header
file.

.. code-block::

   vim Coupled/inlet_test.h
   #define ENKF_RESTART

Run the setup script
====================

.. code-block::

   chmod 777 coawst.bash 
   qsub -X -I -l select=1:ncpus=36:mpiprocs=36 -l walltime=01:00:00 -q regular -A $DARES_PROJECT
   ./coawst.bash -j 36

Edit the configuration files
============================

The ``coupling_<COAWST project name>.in`` file, in this case the
``coupling_inlet_test.in`` file, should set the number of processors allocated
to each of the component models in the experiment.

.. important::

   Using the term "Nodes" here is a misnomer. In actuality, the user is setting
   the number of processors for each component model.

This experiment is being run on a single node of Cheyenne, which has 36
processors. There are two component models, ROMS and SWAN, so ``NnodesWAV`` and
``NnodesOCN`` should be set in a manner that adds up to 36.

.. code-block::

   vim Coupled/coupling_inlet_test.in
   41   NnodesWAV =  11                    ! wave model
   42   NnodesOCN =  25                    ! ocean model
   [ ... ]
   61    WAV_name = /glade/work/$USER/git/COAWST/Projects/Inlet_test/Coupled/swan_inlet_test.in      ! wave model
   62    OCN_name = /glade/work/$USER/git/COAWST/Projects/Inlet_test/Coupled/ocean_inlet_test.in     ! ocean model

Since 25 processors were set for ``NnodesOCN`` in the
``coupling_inlet_test.in`` file, the product of ``NtileI`` and ``NtileJ`` in
the ``ocean_<COAWST project name.>.in`` file, in this case the
``ocean_inlet_test.in`` file, should equal 25.

.. code-block::

   vim Coupled/ocean_inlet_test.in
   111    NtileI == 5                                ! I-direction partition
   112    NtileJ == 5                                ! J-direction partition
   
Running the executable
======================

Now that those configuration files are set, the executable can be run.

.. code-block::
   
   cd $COAWST_ROOT
   mpirun -np 36 ./coawstM Projects/Inlet_test/Coupled/coupling_inlet_test.in
   [ ... ]
   --------------------------------------------------------------------------------
   Model Input Parameters:  ROMS/TOMS version 3.9
                            Wednesday - December 7, 2022 -  1:37:22 PM
   --------------------------------------------------------------------------------
   [ ... ]
   ROMS/TOMS: DONE... Wednesday - December 7, 2022 -  1:40:34 PM
   ls -lart
   [ ... ]
   ocean_dai.nc
   [ ... ]

COAWST runs properly and outputs an ``ocean_dai.nc`` restart file.

