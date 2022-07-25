#################################
Compile and stage your experiment
#################################

After generating your initial ensemble and staging ``obs_seq`` files for 
assimilation, you're ready to compile and stage your experiment.

The ``shell_scripts`` subdirectory in the ROMS model directory contains scripts
that you can adapt in order to stage your experiment.

.. code-block::

   cd DART/models/ROMS/shell_scripts
   ls
   advance_ensemble.csh.template
   batch_job_resource_explanation.txt
   cycle.csh.template
   ensemble.sh
   get_ocean_time.csh
   run_filter.csh.template
   stage_experiment.csh
   submit_multiple_cycles_lsf.csh
   submit_multiple_jobs_slurm.csh

The ``stage_experiment.csh`` shell script is the primary script you will be 
adapting to build your experiment. 

Ensure to add a new ``hostname`` case at the top of the script corresponding
to the hostname of your computing cluster. Add directory paths corresponding
to the appropriate paths on your machine.

There are scripts for submitting jobs on both ``LSF`` and ``SLURM`` job 
management systems. If your cluster uses ``PBS`` or some alternative system,
you will need to adapt one of those scripts for your system.

For more detailed information, read the `Shell scripts <https://docs.dart.ucar.edu/en/latest/models/ROMS/readme.html#shell-scripts>`_ section of DART's ROMS interface documentation.
