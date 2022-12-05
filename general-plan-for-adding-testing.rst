###########################################
General plan for adding and testing a model
###########################################

Overview
========

This project uses the `Coupled-Ocean-Atmosphere-Wave-Sediment Transport
(COAWST) <https://www.usgs.gov/centers/whcmsc/science/coawst-coupled-ocean-atmosphere-wave-sediment-transport-modeling-system>`_ modeling system. COAWST includes many model components and uses the
Model Coupling Toolkit (MCT) to couple the components together. The primary
model components are:

- the Weather Research and Forecasting (WRF) model for the atmosphere
- the Regional Ocean Modeling System (ROMS) model for the ocean
- the Simulating WAves Nearshore (SWAN) model for ocean surface waves

As a starting point, we will focus on the ROMS component of COAWST. The Ocean
and Atmosphere Studies Laboratory provides a `user guide for COAWST in which
the ROMS section begins on page 53 <http://mtc-m21c.sid.inpe.br/col/sid.inpe.br/mtc-m21c/2020/10.02.15.11/doc/publicacao.pdf>`_.

General steps
=============

First stage: ensure the basic functioning of the system using synthetic
observations.

1. Compile a single instance of the model that can be used in an observing
   system simulation experiment (OSSE). This step is covered in depth in the
   :doc:`osse-pmo` page.
2. Generate an ensemble of model states. This step is covered in the
   :doc:`generate-initial-ensemble` page.
3. Use the synthetic observations from the OSSE to run a test assimilation.

Second stage: attemp to assimilate real observations.

1. Find a suitable `observation converter <https://docs.dart.ucar.edu/en/latest/observations/obs_converters/README.html#converter-programs>`_ in the DART repository, as described in the
   :doc:`create-obs-seq-file` page, or
2. Write an observation converter for your observations.

