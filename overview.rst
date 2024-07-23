#########################
ROMS Interpolate Overview
#########################

The ``model_mod`` in the main branch of the DART repository relies on ROMS to 
generate verification observations (known as precomputed forward operators in
DART parlance) in the output of s4dvar.in:MODname.

This process is problematic because ROMS doesn't appear to have complete
coverage in its code integration testing for all of the CPP options and thus 
recent commits of ROMS won't compile with the ``ENKF_RESTART`` CPP option
defined.
