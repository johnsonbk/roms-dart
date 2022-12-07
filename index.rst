.. Empty Sphinx Project documentation master file, created by
   sphinx-quickstart on Fri Oct 30 17:26:59 2020.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

############
Introduction
############

These pages document how to use DART's interface to the Regional Ocean Modeling
System (ROMS).

What you're about to do
=======================

The majority of the infrastructure needed to complete an assimilation cycle is
already implemented in the DART source code, so these instructions will show 
you how to work with the existing code to carry out your experiment. This
diagram provides a summary of a single assimilation cycle:

|assimilation_cycle|

.. |assimilation_cycle| image:: /_static/assimilation-cycle.png

In this diagram, the blue/green trapezoids represent an ensemble of model
states. The observations to be assimilated are converted to DART's observation
sequence, or ``obs_seq``, file format.

The steps needed to complete an assimilation cycle are:

1. :doc:`compile-dart-executables`.
2. :doc:`generate-initial-ensemble` using the ``perturb_single_instance``
   executable.
3. :doc:`create-obs-seq-file` using a suitable observation converter.
4. :doc:`compile-and-stage-experiment` using the build scripts in the ROMS
   subdirectory of the DART repository.


.. toctree::
   :maxdepth: 2
   :hidden:
   :caption: Contents

   /compile-dart-executables
   /compile-coawst-cheyenne

.. toctree::
   :maxdepth: 2
   :hidden:
   :caption: Goals for Jose's visit

   /general-plan-for-adding-testing
   /osse-pmo
   /generate-initial-ensemble
   /create-obs-seq-file
   /compile-and-stage-experiment
   
