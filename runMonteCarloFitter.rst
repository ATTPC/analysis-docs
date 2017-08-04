Run the Monte Carlo Fitter on the Tracks
========================================

Implementation
--------------

**This step in the analysis should be performed on an HPC.** The script for the `runfit` Monte Carlo fitter is in the main pytpc repository (pytpc/bin/runfit) and the implementation in the Python module `pytpc.fitting`. The `runfit` script uses the Monte Carlo algorithm to fit a (cleaned) HDF5 data file and writes the output to a SQLite database.

The "Fitting parameters" in the config file include values for the initial particle state as well as the fitting algorithm itself.

Usage
-----

`runfit` can be used as follows:

.. code-block:: shell

   runfit [-h] --config CONFIG [--config-patch CONFIG_PATCH]
              [--evtlist EVTLIST] [--verbose]
              input_file db_path
              # arguments in [] are optional

The `CONFIG` argument takes the path to the proper config file. The `CONFIG_PATCH` argument allows specific values in the configuration file to be overridden using small YAML file patches (see :ref:`configPatchingSection`). The `EVTLIST` argument takes a path leading to an HDF5 file containing a list of good events. The `--verbose` flag causes more output to be printed to the screen. The `input_file` argument takes the path to the HDF5 file to be fit and the `db_path` takes the path to the output database file (.db). If no path, only a filename, is provided, a SQLite database file will be created in the same directory as the original HDF5.

The following command will display this information in the terminal:

.. code-block:: shell

   runfit -h
