##########################################################
Observing system simulation experiment / perfect_model_obs
##########################################################


The DART executable ``perfect_model_obs`` takes restart files from a single
instance of a model run and creates observation sequence files from it 
containing synthetic "observations" from the model run.

The most straightforward way to use ``perfect_model_obs`` is to use two
precursor programs to format a file that can be fed to ``perfect_model_obs``:

1. ``create_obs_sequence`` creates a template for the observations
2. ``create_fixed_network_sequence`` repeats that template at multiple times
3. ``perfect_model_obs`` harvests the observation values.

.. note::

   The model_mod requires the ``s_rho`` field in order to run perfect_model_obs.
   It is not present in the ``wc13_grd.nc`` file, however, it is present in the
   restart from a given ROMS run.

|perfect-model-obs|

  .. |perfect-model-obs| image:: /_static/perfect-model-obs.gif
