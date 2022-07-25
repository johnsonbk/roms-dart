######################################
Generate an ensemble of initial states
######################################

The DART executable ``perturb_single_instance`` takes a single ROMS restart 
file as input and will generate additional copies of the restart file with 
small perturbations to the first file, in order to generate an ensemble of 
initial model states.

|perturb-single-instance|
 
 .. |perturb-single-instance| image:: /_static/perturb-single-instance.png

You should always use a restart file from your experimental domain. But for
demonstration purposes, DART profiles an archive that contains several restart
files. To download the demo archive:

.. code-block::

   cd DART/models/ROMS/work
   wget https://www.image.ucar.edu/pub/DART/ROMS/dart_roms_test_data.tar.gz
   tar -xzvf dart_roms_test_data.tar.gz

This will extract a directory named ``wc12`` that contains ``.in`` files and
netCDF files from the West Coast domain described in Moore et al. (2020) [1]_.

.. code-block::

   ls wc12
   ocean.in			varinfo.dat
   roms_posterior_0001_37700.nc	wc12_avg.nc
   roms_posterior_0002_37700.nc	wc12_dia.nc
   roms_posterior_0003_37700.nc	wc12_his.nc
   roms_posterior_0004_37700.nc	wc12_ini_0001.nc
   s4dvar.in

The ``perturb_single_instance`` executable expects the initial input file to be
named ``roms_input.nc`` so use a symbolic link to associate one of the netCDF
restart files in wc12 with that name.

.. code-block::

   ln -s wc12/roms_posterior_0001_37700.nc roms_input.nc

Next, add a namelist in the ``input.nml`` file named
``perturb_single_instance_nml`` to instruct the executable how many perturbed 
copies it should create. Open ``input.nml`` with a text editor and add a 
namlist similar to:

.. code-block::

   &perturb_single_instance_nml
      ens_size               = 3
      input_files            = 'roms_input.nc'
      output_files           = 'wc12/roms_posterior_0005_37700.nc','wc12/roms_posterior_0006_37700.nc','wc12/roms_posterior_0007_37700.nc'
      output_file_list       = ''
      perturbation_amplitude = 0.2
    /

Save the namelist and run the executable:

.. code-block::

   ./perturb_single_instance

You should see as many perturbed files as you specified in the namelist:

.. code-block::

   ls -art wc12
   [ ...  ]
   roms_posterior_0005_37700.nc
   roms_posterior_0006_37700.nc
   roms_posterior_0007_37700.nc

References
==========

.. [1] Moore, A., J. Zavala-Garay, H. G. Arango, C. A. Edwards, J. Anderson,
       and T. Hoar, 2020: Regional and basin scale applications of ensemble
       adjustment Kalman filter and 4D-Var ocean data assimilation systems.
       *Progress in Oceanography*, **189**, 102450,
       https://doi.org/10.1016/j.pocean.2020.102450.

