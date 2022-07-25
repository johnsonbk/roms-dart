#############################################################
Convert your observations into DART's ``obs_seq`` file format
#############################################################

DART has a proprietary file format used to contain observations called an
``obs_seq`` or observation sequence file.

This file format is necessary because ensemble data assimilation techniques
require that each observation has a specified observation error variance
in order to apply Bayes' Theorem during the assimilation.

DART provides source code to compile a type of executable known as an
observation converter that takes source data and creates a suitable ``obs_seq``
file for assimilation.

|observation-converter|

.. |observation-converter| image:: /_static/observation-converter.png

It is possible that there is already source code in the repository to compile
an observation converter for the data you intend to assimilate.

Examine the ``obs_converters`` directory to see if there is already a converter
for your source data:

.. code-block::

   cd DART/observations/obs_converters
   ls
   AIRS          GSI2DART        SIF
   Ameriflux     GTSPP           snow
   AURA          MADIS           SSEC
   AVISO         MIDAS           SST
   CHAMP         MODIS           SSUSI
   cice          MPD             tec
   CNOFS         NASA_Earthdata  test_obs
   CONAGUA       NCEP            text
   COSMOS        NSIDC           text_GITM
   DWL           obs_error       tpw
   even_sphere   ocean_color     tropical_cyclone
   GMI           ok_mesonet      USGS
   gnd_gps_vtec  quikscat        utilities
   GOES          radar           var
   gps           README.rst      WOD
   GPSPW         ROMS
   GRACE         SABER

Comprehensive descriptions are also available in the `Available observation
converter programs page <https://docs.dart.ucar.edu/en/latest/guide/available-observation-converters.html>`_
in the DART documentation.

