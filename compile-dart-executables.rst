#############################################
Compile DART executables in ROMS subdirectory
#############################################

Setting your mkmf.template
==========================

If you haven't done so already, change directory to the ``build_templates``
subdirectory:

.. code-block::

   cd DART/build-templates
   ls mkmf.*
   mkmf.template.absoft.osx       mkmf.template.pgi.cray
   mkmf.template.g95              mkmf.template.pgi.linux
   mkmf.template.gfortran         mkmf.template.pgi.osx
   mkmf.template.intel.linux      mkmf.template.rttov.gfortran
   mkmf.template.intel.osx        mkmf.template.rttov.intel
   mkmf.template.lahey.linux      mkmf.template.sgi.altix
   mkmf.template.nag.linux        mkmf.template.xlf.aix
   mkmf.template.pathscale.linux

and identify which ``mkmf.template`` is suitable for your system. For example
NCAR's `Cheyenne supercomputer <https://arc.ucar.edu/knowledge_base/70549542>`_
is built using Intel processors and uses the Linux operating system, so it
should use the `mkmf.template.intel.linux` template.

.. code-block::

   mv mkmf.template.intel.linux mkmf.template
   
The ``$NETCDF`` environmental variable is already set on this system, so it 
doesn't need to be set within the ``mkmf.template``:

.. code-block::

   echo $NETCDF
   /glade/u/apps/ch/opt/netcdf/4.8.1/intel/19.1.1/

If the ``$NETCDF`` environmental variable isn't set on your system, identify 
the path to your netCDF installation and set it in your ``mkmf.template``.

Compiling the ROMS executables
==============================

Next, change directory to the ROMS work subdirectory:

.. code-block::

   cd ../models/ROMS/work
   ls
   input.nml           obs_seq_fo.out  ocean.in.template  s4dvar.in.template
   input.nml.template  obs_seq.out     quickbuild.sh

The ``quickbuild.sh`` script will compile all of the necessary DART executables
for ROMS.

.. code-block::

   ./quickbuild.sh
   [ ... ]
   ls
   advance_time              Makefile           obs_seq_verify
   closest_member_tool       model_mod_check    ocean.in.template
   create_fixed_network_seq  obs_common_subset  perfect_model_obs
   create_obs_sequence       obs_diag           perturb_single_instance
   dart_log.nml              obs_selection      preprocess
   dart_log.out              obs_seq_coverage   quickbuild.sh
   fill_inflation_restart    obs_seq_fo.out     s4dvar.in.template
   filter                    obs_seq.out        wakeup_filter
   input.nml                 obs_seq_to_netcdf
   input.nml.template        obs_sequence_tool

If ``quickbuild.sh`` ran properly, you should now see the work directory 
populated with newly built executables.

